# Escanear Barcode Scanner y Ionic 7

[Crear aplicación con Barcode](https://ionic.io/blog/how-to-build-an-ionic-barcode-scanner-with-capacitor)

### GUIA
1. Crear aplicación de forma normal en ionic (testeado con ngmodules)

2. Instalar la libraría.
`npm install @capacitor-mlkit/barcode-scanning`

3. Personalizar la página donde se escanea con un botoón para acceder a la función.

4. Importar en el .ts de la página.
`import { Barcode, BarcodeScanner } from '@capacitor-mlkit/barcode-scanning';`

5. Configurar el .ts de la página

```
import { Component, OnInit } from '@angular/core';
import { Barcode, BarcodeScanner } from '@capacitor-mlkit/barcode-scanning';
import { AlertController } from '@ionic/angular';

@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})
export class HomePage implements OnInit{

  isSupported = false;
  barcodes: Barcode[] = [];

  constructor(private alertController: AlertController) {}
  ngOnInit(){
    BarcodeScanner.isSupported().then((result) => {
      this.isSupported = result.supported;
    });
  }

  async scan(): Promise<void> {
    const granted = await this.requestPermissions();
    if (!granted) {
      this.presentAlert();
      return;
    }
    const { barcodes } = await BarcodeScanner.scan();
    this.barcodes.push(...barcodes);
  }

  async requestPermissions(): Promise<boolean> {
    const { camera } = await BarcodeScanner.requestPermissions();
    return camera === 'granted' || camera === 'limited';
  }

  async presentAlert(): Promise<void> {
    const alert = await this.alertController.create({
      header: 'Permiso denegado',
      message: 'Para usar la aplicación autorizar los permisos de cámara',
      buttons: ['OK'],
    });
    await alert.present();
  }
}
```

6. Crear aplicación de android
`ionic cap add android`

7. Construir con build
`ionic build`

8. Configurar manifiesto para dar permiso a la camara. Agregar antes de cerrar etiqueta manifest
```
<!-- To get access to the camera. -->
<uses-permission android:name="android.permission.CAMERA" />
<!-- To get access to the flashlight. -->
<uses-permission android:name="android.permission.FLASHLIGHT"/>
```

y dentro de la etiqueta **aplication** agregar
```
<meta-data android:name="com.google.mlkit.vision.DEPENDENCIES" android:value="barcode_ui"/>
```

9. Agregar los cambios a la aplicación
`ionic cap copy android`


10. Sincronizar
`ionic cap sync`