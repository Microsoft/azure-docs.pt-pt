---
title: Segurança e autenticação-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Segurança e autenticação na grade de eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 77b4b265b2e993ccdbc9e07fd2dab5a37ed22a6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992161"
---
# <a name="security-and-authentication"></a>Segurança e autenticação

Segurança e autenticação é um conceito avançado e requer familiaridade com noções básicas da grade de eventos primeiro. Comece [aqui](concepts.md) se você for novo na grade de eventos em IOT Edge. O módulo de grade de eventos baseia-se na infraestrutura de segurança existente no IoT Edge. Consulte [esta documentação](../../iot-edge/security.md) para obter detalhes e configuração.

As seções a seguir descrevem detalhadamente como essas configurações são protegidas e autenticadas:-

* Configuração de TLS
* Autenticação de cliente de entrada
* Autenticação de servidor de saída
* Autenticação de cliente de saída

>[!IMPORTANT]
>A segurança e a autenticação do módulo de grade de eventos aproveitam a infraestrutura existente disponível em IoT Edge. A suposição é que IoT Edge subsistema é seguro.

>[!IMPORTANT]
>A configuração da grade **de eventos é segura por padrão**. As subseções a seguir explicam todas as opções e possíveis valores que você pode usar para substituir os aspectos de autenticação. Entenda o impacto antes de fazer alterações. Para que as alterações entrem em vigor, o módulo de grade de eventos precisará ser reimplantado.

## <a name="tls-configuration-aka-server-authentication"></a>Configuração de TLS (autenticação de servidor a. k. a)

O módulo de grade de eventos hospeda pontos de extremidade HTTP e HTTPS. Cada módulo de IoT Edge recebe um certificado de servidor pelo daemon de segurança do IoT Edge. Usamos o certificado do servidor para proteger o ponto de extremidade. Na expiração, o módulo é atualizado automaticamente com um novo certificado do daemon de segurança IoT Edge.

Por padrão, somente a comunicação HTTPS é permitida. Você pode substituir esse comportamento por meio de **entrada: serverAuth:** configuração de tlsPolicy. A tabela a seguir captura os possíveis valores dessa propriedade.

| Valor (es) possível (is) | Descrição |
| ---------------- | ------------ |
| Restrito | Predefinição. Habilita somente HTTPS
| Ativado | Habilita HTTP e HTTPS
| Desativado | Habilita somente HTTP

## <a name="inbound-client-authentication"></a>Autenticação de cliente de entrada

Os clientes são entidades que fazem operações de gerenciamento e/ou tempo de execução. Os clientes podem ser outros módulos IoT Edge, aplicativos não IoT.

O módulo de grade de eventos dá suporte a dois tipos de autenticação de cliente:-

* Baseada em chave de SAS (assinatura de acesso compartilhado)
* com base em certificado

Por padrão, o módulo de grade de eventos é configurado para aceitar somente a autenticação baseada em certificado. Na inicialização, o módulo de grade de eventos recupera "TrustBundle" do daemon de segurança IoT Edge e o usa para validar qualquer certificado de cliente. Os certificados de cliente que não são resolvidos para esta cadeia serão rejeitados com `UnAuthorized`.

### <a name="certificate-based-client-authentication"></a>Autenticação de cliente baseada em certificado

A autenticação baseada em certificado está ativada por padrão. Você pode optar por desabilitar a autenticação baseada em certificado por meio da propriedade de **entrada: clientAuth: clientCert: Enabled**. A tabela a seguir captura possíveis valores.

| Valor (es) possível (is) | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Exige que todas as solicitações no módulo da grade de eventos apresentem um certificado de cliente. Além disso, será necessário configurar a **entrada: clientAuth: clientCert: Source**.
| false | Não force um cliente a apresentar o certificado.

A tabela a seguir captura possíveis valores para **entrada: clientAuth: clientCert: Source**

| Valor (es) possível (is) | Descrição |
| ---------------- | ------------ |
| IoT Edge | Predefinição. Usa o Trustbundle da IoT Edge para validar todos os certificados de cliente.

Se um cliente apresentar uma assinatura automática, por padrão, o módulo de grade de eventos rejeitará essas solicitações. Você pode optar por permitir certificados de cliente autoassinados por meio da propriedade de **entrada: clientAuth: clientCert: allowUnknownCA** . A tabela a seguir captura possíveis valores.

| Valor (es) possível (is) | Descrição |
| ----------------  | ------------|
| true | Predefinição. Permite que certificados autoassinados sejam apresentados com êxito.
| false | Falhará nas solicitações se os certificados autoassinados forem apresentados.

>[!IMPORTANT]
>Em cenários de produção, talvez você queira definir a **entrada: clientAuth: clientCert: allowUnknownCA** como **false**.

### <a name="sas-key-based-client-authentication"></a>Autenticação de cliente baseada em chave SAS

Além da autenticação baseada em certificado, o módulo de grade de eventos também pode fazer a autenticação baseada em chave SAS. A chave SAS é como um segredo configurado no módulo de grade de eventos que ele deve usar para validar todas as chamadas de entrada. Os clientes precisam especificar o segredo no cabeçalho HTTP ' AEG-SAS-Key '. A solicitação será rejeitada com `UnAuthorized` se não corresponder.

A configuração para controlar a autenticação baseada em chave SAS é de **entrada: clientAuth: sasKeys: Enabled**.

| Valor (es) possível (is) | Descrição  |
| ----------------  | ------------ |
| true | Permite a autenticação baseada em chave SAS. Requer **entrada: clientAuth: sasKeys: key1** ou de **entrada: clientAuth: sasKeys: Key2**
| false | Predefinição. A autenticação baseada em chave SAS está desabilitada.

 **entrada: clientAuth: sasKeys: key1** e **entrada: clientAuth: sasKeys: Key2** são chaves que você configura o módulo de grade de eventos para verificar as solicitações de entrada. Pelo menos uma das chaves precisa ser configurada. O cliente que estiver fazendo a solicitação precisará apresentar a chave como parte do cabeçalho de solicitação '**AEG-SAS-Key**'. Se ambas as chaves estiverem configuradas, o cliente poderá apresentar qualquer uma das chaves.

> [!NOTE]
>Você pode configurar os dois métodos de autenticação. Nesse caso, a chave SAS é verificada primeiro e somente se isso falhar, a autenticação baseada em certificado será executada. Para que uma solicitação seja realizada com sucesso, apenas um dos métodos de autenticação precisa ter sucesso.

## <a name="outbound-client-authentication"></a>Autenticação de cliente de saída

O cliente no contexto de saída refere-se ao módulo de grade de eventos. A operação que está sendo feita é fornecer eventos aos assinantes. Os módulos de assinatura são considerados como o servidor.

Cada módulo de IoT Edge recebe um certificado de identidade pelo daemon de segurança do IoT Edge. Usamos o certificado de identidade para chamadas de saída. Na expiração, o módulo é atualizado automaticamente com um novo certificado do daemon de segurança IoT Edge.

A configuração para controlar a autenticação de cliente de saída é de **saída: clientAuth: clientCert: Enabled**.

| Valor (es) possível (is) | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Requer que todas as solicitações de saída do módulo de grade de eventos apresentem um certificado. Precisa configurar a **saída: clientAuth: clientCert: Source**.
| false | Não exija que o módulo de grade de eventos apresente seu certificado.

A configuração que controla a origem do certificado é de **saída: clientAuth: clientCert: Source**.

| Valor (es) possível (is) | Descrição |
| ---------------- | ------------ |
| IoT Edge | Predefinição. Usa o certificado de identidade do módulo configurado pelo daemon de segurança do IoT Edge.

### <a name="outbound-server-authentication"></a>Autenticação de servidor de saída

Um dos tipos de destino para um assinante de grade de eventos é "webhook". Por padrão, somente pontos de extremidade HTTPS são aceitos para tais assinantes.

A configuração para controlar a política de destino do webhook de **saída: webhook: httpsOnly**.

| Valor (es) possível (is) | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Permite somente assinantes com ponto de extremidade HTTPS.
| false | Permite assinantes com o ponto de extremidade HTTP ou HTTPS.

Por padrão, o módulo de grade de eventos validará o certificado do servidor do Assinante. Você pode ignorar a validação substituindo **saída: webhook: skipServerCertValidation**. Os valores possíveis são:-

| Valor (es) possível (is) | Descrição |
| ----------------  | ------------ |
| true | Não valide o certificado do servidor do Assinante.
| false | Predefinição. Validar o certificado do servidor do Assinante.

Se o certificado do assinante for autoassinado, por padrão, o módulo de grade de eventos rejeitará esses assinantes. Para permitir o certificado autoassinado, você pode substituir **saída: webhook: allowUnknownCA**. A tabela a seguir captura os possíveis valores.

| Valor (es) possível (is) | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Permite que certificados autoassinados sejam apresentados com êxito.
| false | Falhará nas solicitações se os certificados autoassinados forem apresentados.

>[!IMPORTANT]
>Em cenários de produção, você desejará definir a **saída: webhook: allowUnknownCA** como **false**.

> [!NOTE]
>IoT Edge ambiente gera certificados autoassinados. A recomendação é gerar certificados emitidos por CAs autorizadas para cargas de trabalho de produção e definir a propriedade **allowUnknownCA** na entrada e saída para **false**.

## <a name="summary"></a>Resumo

Um módulo de grade de eventos é **seguro por padrão**. Recomendamos manter esses padrões para suas implantações de produção.

A seguir estão os princípios de orientação a serem usados durante a configuração:-

* Permitir somente solicitações HTTPS no módulo.
* Permitir somente autenticação de cliente baseada em certificado. Permitir somente os certificados emitidos por CAs conhecidas. Não permitir certificados autoassinados.
* Não permitir autenticação de cliente baseada em SASKey.
* Sempre apresente o certificado de identidade do módulo de grade de eventos em chamadas de saída.
* Permitir somente assinantes HTTPS para tipos de destino do webhook.
* Sempre validar o certificado do servidor do assinante para tipos de destino do webhook. Permitir somente certificados emitidos por CAs conhecidas. Não permitir certificados autoassinados.

Por padrão, o módulo de grade de eventos é implantado com a seguinte configuração:-

 ```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
