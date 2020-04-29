---
title: Segurança e autenticação - Azure Event Grid IoT Edge [ Microsoft Docs
description: Segurança e autenticação em Grelha de Eventos na Borda IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844518"
---
# <a name="security-and-authentication"></a>Segurança e autenticação

A segurança e a autenticação são um conceito avançado e requer familiaridade com os fundamentos da Grelha de Eventos em primeiro lugar. Comece [aqui](concepts.md) se for novo na Grelha de Eventos em IoT Edge. O módulo Event Grid baseia-se na infraestrutura de segurança existente no IoT Edge. Consulte [esta documentação](../../iot-edge/security.md) para mais detalhes e configuração.

As seguintes secções descrevem em pormenor como estas definições são seguras e autenticadas:

* Configuração TLS
* Autenticação do cliente de entrada
* Autenticação do servidor de saída
* Autenticação de cliente de saída

>[!IMPORTANT]
>A segurança e a alavancagem de autenticação do módulo Event Grid é a infraestrutura existente disponível no IoT Edge. O pressuposto é que o subsistema IoT Edge é seguro.

>[!IMPORTANT]
>A configuração da Grelha de Eventos é **segura por defeito**. As seguintes subsecções explicam todas as opções e possíveis valores que pode utilizar para sobrepor aspetos da autenticação. Compreenda o impacto antes de fazer quaisquer alterações. Para que quaisquer alterações entrem em vigor, o módulo Da Grelha de Eventos terá de ser reimplantado.

## <a name="tls-configuration-aka-server-authentication"></a>Configuração TLS (também k.k.a autenticação do servidor)

O módulo De Grelha de Eventos acolhe os pontos finais HTTP e HTTPS. Todos os módulos IoT Edge são atribuídos a um certificado de servidor pelo daemon de segurança do IoT Edge. Usamos o certificado do servidor para proteger o ponto final. No prazo de validade, o módulo atualiza-se automaticamente com um novo certificado do daemon de segurança IoT Edge.

Por predefinição, apenas é permitida a comunicação HTTPS. Pode anular este comportamento através **inbound__serverAuth__tlsPolicy** configuração. A tabela seguinte capta o valor possível deste imóvel.

| Valores possíveis | Descrição |
| ---------------- | ------------ |
| Estrito | Predefinição. Só permite HTTPS
| Ativado | Permite tanto http como HTTPS
| Desativado | Permite apenas HTTP

## <a name="inbound-client-authentication"></a>Autenticação do cliente de entrada

Os clientes são entidades que fazem operações de gestão e/ou tempo de execução. Os clientes podem ser outros módulos IoT Edge, aplicações não IoT.

O módulo Event Grid suporta dois tipos de autenticação do cliente:

* Assinatura de acesso partilhado (SAS) baseada na chave
* com base em certificados

Por predefinição, o módulo De Rede de Eventos está configurado para aceitar apenas a autenticação baseada em certificados. No arranque, o módulo Event Grid recupera o "TrustBundle" da IoT Edge security daemon e usa-o para validar qualquer certificado de cliente. Os certificados de cliente que não se `UnAuthorized`resolvam a esta cadeia serão rejeitados com .

### <a name="certificate-based-client-authentication"></a>Autenticação de cliente baseada em certificado

A autenticação baseada em certificado é por defeito. Pode optar por desativar a autenticação baseada em certificados através da **inbound__clientAuth__clientCert__enabled**de propriedade . A tabela que se segue capta um valor possível.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Requer todos os pedidos no módulo Event Grid para apresentar um certificado de cliente. Além disso, terá de configurar **inbound__clientAuth__clientCert__source**.
| false | Não obrigue um cliente a apresentar certificado.

A tabela que se segue capta um valor possível para **inbound__clientAuth__clientCert__source**

| Valores possíveis | Descrição |
| ---------------- | ------------ |
| IoT Edge | Predefinição. Usa o Pacote Fiduciário da IoT Edge para validar todos os certificados de cliente.

Se um cliente apresentar um auto-assinado, por padrão, o módulo De Rede de Eventos rejeitará tais pedidos. Pode optar por permitir certificados de cliente auto-assinados através **inbound__clientAuth__clientCert__allowUnknownCA** propriedade. A tabela que se segue capta um valor possível.

| Valores possíveis | Descrição |
| ----------------  | ------------|
| true | Predefinição. Permite que os certificados auto-assinados sejam apresentados com sucesso.
| false | Falhará os pedidos se forem apresentados certificados auto-assinados.

>[!IMPORTANT]
>Em cenários de produção, é possível **que** inbound__clientAuth__clientCert__allowUnknownCA seja **falsa.**

### <a name="sas-key-based-client-authentication"></a>Autenticação do cliente baseada na chave SAS

Além da autenticação baseada em certificado, o módulo De Rede de Eventos também pode fazer autenticação baseada em Chaves SAS. A tecla SAS é como um segredo configurado no módulo De Rede de Eventos que deve usar para validar todas as chamadas recebidas. Os clientes precisam especificar o segredo no cabeçalho HTTP 'aeg-sas-key'. O pedido será `UnAuthorized` rejeitado se não corresponder.

A configuração para controlar a autenticação baseada na chave SAS é **inbound__clientAuth__sasKeys__enabled**.

| Valores possíveis | Descrição  |
| ----------------  | ------------ |
| true | Permite a autenticação baseada na chave SAS. Requer **inbound__clientAuth__sasKeys__key1** ou **inbound__clientAuth__sasKeys__key2**
| false | Predefinição. A autenticação baseada na chave SAS está desativada.

 **inbound__clientAuth__sasKeys__key1** e **inbound__clientAuth__sasKeys__key2** são chaves que configura o módulo Da Grelha de Eventos para verificar contra os pedidos de entrada. Pelo menos uma das chaves precisa de ser configurada. O cliente que efaça o pedido terá de apresentar a chave como parte do cabeçalho de pedido '**aeg-sas-key**'. Se ambas as teclas estiverem configuradas, o cliente pode apresentar qualquer uma das chaves.

> [!NOTE]
>Pode configurar ambos os métodos de autenticação. Neste caso, a chave SAS é verificada primeiro e só se tal falhar, a autenticação baseada no certificado é realizada. Para que um pedido seja bem sucedido, apenas um dos métodos de autenticação tem de ser bem sucedido.

## <a name="outbound-client-authentication"></a>Autenticação de cliente de saída

Cliente em contexto de saída refere-se ao módulo Event Grid. A operação que está a ser feita está a entregar eventos aos assinantes. Os módulos de subscrição são considerados como o servidor.

Todos os módulos IoT Edge são atribuídos um Certificado de Identidade pelo daemon de segurança do IoT Edge. Usamos o certificado de identidade para chamadas de saída. No prazo de validade, o módulo atualiza-se automaticamente com um novo certificado do daemon de segurança IoT Edge.

A configuração para controlar a autenticação do cliente de saída é **outbound__clientAuth__clientCert__enabled**.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Requer todos os pedidos de saída do módulo Event Grid para apresentar um certificado. Precisa de configurar **outbound__clientAuth__clientCert__source.**
| false | Não exija que o módulo Da Grelha de Eventos apresente o seu certificado.

A configuração que controla a fonte do certificado é **outbound__clientAuth__clientCert__source**.

| Valores possíveis | Descrição |
| ---------------- | ------------ |
| IoT Edge | Predefinição. Utiliza o certificado de identidade do módulo configurado por IoT Edge security daemon.

### <a name="outbound-server-authentication"></a>Autenticação do servidor de saída

Um dos tipos de destino para um assinante da Rede de Eventos é "Webhook". Por predefinição, apenas são aceites pontos finais HTTPS para esses subscritores.

A configuração para controlar a política de destino webhook **outbound__webhook__httpsOnly**.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Só permite assinantes com ponto final HTTPS.
| false | Permite aos assinantes com ponto final HTTP ou HTTPS.

Por predefinição, o módulo De Rede de Eventos validará o certificado de servidor do assinante. Pode ignorar a validação substituindo **outbound__webhook__skipServerCertValidation**. Os valores possíveis são:

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Não valide o certificado de servidor do assinante.
| false | Predefinição. Valide o certificado de servidor do assinante.

Se o certificado do assinante for auto-assinado, por padrão o módulo DeRede de Eventos rejeitará esses subscritores. Para permitir um certificado auto-assinado, pode anular **outbound__webhook__allowUnknownCA**. A tabela que se segue capta o valor possível.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Permite que os certificados auto-assinados sejam apresentados com sucesso.
| false | Falhará os pedidos se forem apresentados certificados auto-assinados.

>[!IMPORTANT]
>Em cenários de produção, você vai querer definir **outbound__webhook__allowUnknownCA** a **falsos**.

> [!NOTE]
>O ambiente IoT Edge gera certificados auto-assinados. Recomendação é gerar certificados emitidos por CAs autorizados para cargas de trabalho de produção e definir **permitir propriedade UnknownCA** tanto na entrada como na saída para **falso**.

## <a name="summary"></a>Resumo

Um módulo de Grelha de Eventos é **seguro por defeito**. Recomendamos que estes incumprimentos para as suas implementações de produção.

Seguem-se os princípios orientadores a utilizar durante a configuração:

* Permitir apenas pedidos HTTPS no módulo.
* Permitir apenas a autenticação do cliente com base em certificados. Permitir apenas os certificados emitidos por conhecidos CAs. Não permitir certificados auto-assinados.
* Desautorizar a autenticação do cliente baseado em SASKey.
* Apresentar sempre o certificado de identidade do módulo Event Grid em chamadas de saída.
* Permitir apenas assinantes HTTPS para tipos de destino Webhook.
* Valide sempre o certificado de servidor do assinante para tipos de destino Webhook. Permitir apenas certificados emitidos por conhecidos CAs. Não permitir certificados auto-assinados.

Por predefinição, o módulo De Rede de Eventos é implantado com a seguinte configuração:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
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
