
name: Build HEADTRICK APK

on:
  workflow_dispatch:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Baixar arquivos
        uses: actions/checkout@v4

      - name: Configurar Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Configurar Android
        uses: android-actions/setup-android@v3
        with:
          accept-android-sdk-licenses: true

      - name: Aceitar licenças SDK
        run: yes | sdkmanager --licenses

      - name: Instalar SDK
        run: |
          sdkmanager "platforms;android-35" "build-tools;35.0.0"

      - name: Preparar Gradle
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'

      - name: Extrair projeto
        run: |
          mkdir source
          unzip -o HeadtrickPremium_GitHub_Actions.zip -d source

      - name: Gerar APK
        run: gradle -p source assembleDebug

      - name: Disponibilizar APK
        uses: actions/upload-artifact@v4
        with:
          name: HEADTRICK-PREMIUM-APK
          path: source/app/build/outputs/apk/debug/app-debug.apk
