# Site Estático - Deploy Automático para AWS S3 com GitHub Actions

Este projeto demonstra como configurar uma pipeline de **deploy automático** de um site estático para o **Amazon S3** utilizando **GitHub Actions**.

---

## Objetivo

Automatizar o processo de publicação dos arquivos estáticos no bucket S3, garantindo que toda vez que uma alteração for enviada para a branch `main`, o conteúdo seja sincronizado automaticamente, facilitando o fluxo de deploy e reduzindo erros manuais.

---

## Como funciona a pipeline

- O código é versionado e armazenado no GitHub.
- A pipeline é disparada automaticamente em qualquer push para a branch `main`.
- A ação do GitHub Actions usa a [ação jakejarvis/s3-sync-action](https://github.com/jakejarvis/s3-sync-action) para sincronizar os arquivos com o bucket S3.
- As credenciais AWS são mantidas seguras usando os **Secrets** do GitHub.

---

## Estrutura do projeto

- Os arquivos do site estão localizados na pasta raiz (ou em uma subpasta, se for o caso).
- O arquivo do workflow `.github/workflows/deploy.yml` contém a configuração da pipeline.

---

## Configuração necessária

### 1. Criar bucket no AWS S3

- Crie um bucket no Amazon S3.
- Configure as permissões de leitura pública (se o site for público).
- Anote o nome do bucket.

### 2. Criar usuário IAM com permissões para o S3

- Crie um usuário no AWS IAM com permissão para `s3:PutObject`, `s3:GetObject`, `s3:DeleteObject` no bucket criado.
- Gere a Access Key ID e Secret Access Key para esse usuário.

### 3. Configurar Secrets no GitHub

No repositório do projeto, vá em:

`Settings > Secrets and variables > Actions`

Adicione os seguintes secrets:

| Nome                 | Valor                         |
|----------------------|-------------------------------|
| AWS_ACCESS_KEY_ID     | Sua Access Key ID da AWS       |
| AWS_SECRET_ACCESS_KEY | Sua Secret Access Key da AWS   |
| AWS_S3_BUCKET         | Nome do seu bucket S3          |

---

## Uso

Toda vez que um commit for enviado para a branch `main`, a pipeline será executada automaticamente, sincronizando os arquivos para o bucket S3.

---

## Exemplo do arquivo `.github/workflows/deploy.yml`

```yaml
name: Deploy to S3

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Sync to S3
        uses: jakejarvis/s3-sync-action@master
        with:
          args: --delete
        env:
          AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
