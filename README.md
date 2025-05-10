# 🚀 FastAPI Azure Deploy Guide

Bu proje, FastAPI uygulamasını GitHub Actions ile Azure Web App'e deploy etme sürecini anlatır.

## ✅ 1. Azure Kaynaklarını Oluştur
```bash
az group create --name fastapi-rg --location westeurope
az appservice plan create --name fastapi-plan --resource-group fastapi-rg --sku B1 --is-linux
az webapp create --resource-group fastapi-rg --plan fastapi-plan --name fastapi-mirac2025-app --runtime "PYTHON:3.11" --deployment-local-git
```

## ✅ 2. FastAPI Uygulamasını Hazırla
- `main.py` içinde basit uygulama yazılır
- `requirements.txt` içine `fastapi` ve `uvicorn[standard]` yazılır

## ✅ 3. GitHub Repo’ya Push Et
```bash
git init
git remote add origin https://github.com/<kullanici_adin>/<repo_adi>.git
git add .
git commit -m "initial"
git push -u origin master
```

## ✅ 4. Azure Portal’da Deployment Center’ı Ayarla
- App Services > `fastapi-mirac2025-app`
- Deployment Center > GitHub bağlanır, repo ve branch seçilir

## ✅ 5. GitHub Actions YAML Dosyasını Düzenle
`.github/workflows/azure-webapps.yml` içeriği:

```yaml
name: Deploy FastAPI to Azure

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Deploy to Azure Web App
        uses: azure/webapps-deploy@v2
        with:
          app-name: 'fastapi-mirac2025-app'
          publish-profile: ${{ secrets.AZUREAPPSERVICE_PUBLISHPROFILE }}
          package: .
```

## ✅ 6. Startup Komutunu Tanımla
Azure Portal > App Services > Configuration > General Settings:
```bash
python -m uvicorn main:app --host 0.0.0.0 --port 8000
```

## ✅ 7. Uygulaman Yayında
```url
https://fastapi-mirac2025-app.azurewebsites.net/
```
