# Certified Builder Notification

## Descrição

Este projeto é uma função AWS Lambda responsável por processar notificações de certificados gerados. Ele é acionado por mensagens em uma fila SQS, atualiza o status dos certificados e notifica um serviço externo sobre a conclusão.

## Tecnologias Utilizadas

- Python 3.13
- AWS Lambda
- AWS SQS
- AWS DynamoDB
- Boto3
- Pydantic
- HTTPX
- Docker

## Desenvolvimento e Deploy

- **Local**: `docker compose up --build` expõe a Lambda em `http://localhost:9000`.
- **Deploy**: o workflow `.github/workflows/workflow_build.yaml` gera um ZIP e atualiza a função `tech-floripa-certificates-notification-dev`.
- **Infra**: a fila SQS continua ligada pela infraestrutura Terraform; apenas o artefato de código mudou de imagem para ZIP.

## Estrutura do Evento

### Entrada (SQS)

A função Lambda é acionada por um evento SQS que contém um lote de notificações no corpo da mensagem.

**Exemplo de corpo da mensagem SQS:**

```json
{
  "notifications": [
    {
      "order_id": 123456,
      "product_id": 42,
      "product_name": "Demo Event",
      "email": "alex@example.test",
      "certificate_key": "certificates/42/123456/Alex_Example_Demo_Event_ABC-123-XYZ.png",
      "success": true
    }
  ]
}
```

### Saída (Notificação Externa)

Após o processamento, o serviço envia uma notificação para um endpoint externo via POST.

**Exemplo de corpo da requisição POST:**

```json
{
  "product_id": 42,
  "certificates_quantity": 1,
  "certificates": [
    {
      "id": "a1b2c3d4-e5f6-7890-1234-567890abcdef",
      "order_id": 123456,
      "product_id": 42,
      "product_name": "Demo Event",
      "certificate_url": "https://example.com/certificate.png",
      "success": true
    }
  ]
}
```
