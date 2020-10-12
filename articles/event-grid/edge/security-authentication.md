---
title: Segurança e autenticação - Azure Event Grid IoT Edge / Microsoft Docs
description: Segurança e autenticação em Grade de Eventos na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 42f6107ff79d6262cdc0a35cf972cf65d3a9a802
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171385"
---
# <a name="security-and-authentication"></a>Segurança e autenticação

Segurança e autenticação é um conceito avançado e requer familiaridade com o básico da Grade de Eventos primeiro. Comece [aqui](concepts.md) se for novo na Grade de Eventos em IoT Edge. O módulo Event Grid baseia-se na infraestrutura de segurança existente no IoT Edge. Consulte [esta documentação](../../iot-edge/security.md) para mais detalhes e configuração.

As seguintes secções descrevem em detalhe como estas definições são protegidas e autenticadas:

* Configuração TLS
* Autenticação de clientes de entrada
* Autenticação do servidor de saída
* Autenticação de clientes de saída

>[!IMPORTANT]
>A segurança do módulo de event Grid e a alavancagem de autenticação são a infraestrutura existente disponível no IoT Edge. O pressuposto é que o subsiste IoT Edge está seguro.

>[!IMPORTANT]
>A configuração da Grelha de Eventos é **segura por predefinição**. As seguintes subsecções explicam todas as opções e possíveis valores que pode utilizar para anular aspetos da autenticação. Compreenda o impacto antes de fazer quaisquer alterações. Para que quaisquer alterações entrem em vigor, o módulo 'Grade de Eventos' terá de ser redistribuído.

## <a name="tls-configuration-aka-server-authentication"></a>Configuração TLS (também conhecida como autenticação do servidor)

O módulo De Grelha de Eventos acolhe pontos finais HTTP e HTTPS. Cada módulo IoT Edge é atribuído a um certificado de servidor pelo daemon de segurança do IoT Edge. Usamos o certificado do servidor para proteger o ponto final. Ao expirar, o módulo atualiza-se automaticamente com um novo certificado do daemon de segurança IoT Edge.

Por predefinição, apenas é permitida a comunicação HTTPS. Pode anular este comportamento através  **inbound__serverAuth__tlsPolicy** configuração. A tabela seguinte capta os possíveis valores deste imóvel.

| Valores possíveis | Descrição |
| ---------------- | ------------ |
| Estrito | Predefinição. Permite apenas HTTPS
| Ativado | Permite http e HTTPS
| Desativado | Permite apenas HTTP

## <a name="inbound-client-authentication"></a>Autenticação de clientes de entrada

Os clientes são entidades que fazem operações de gestão e/ou tempo de execução. Os clientes podem ser outros módulos IoT Edge, aplicações não-IoT.

O módulo Event Grid suporta dois tipos de autenticação do cliente:

* Assinatura de acesso compartilhado (SAS) baseada em chaves
* certificado

Por predefinição, o módulo 'Grade de Evento' está configurado para aceitar apenas a autenticação baseada em certificados. No arranque, o módulo Event Grid recupera "TrustBundle" do daemon de segurança IoT Edge e usa-o para validar qualquer certificado de cliente. Os certificados de cliente que não se resolvem a esta cadeia serão rejeitados com `UnAuthorized` .

### <a name="certificate-based-client-authentication"></a>Autenticação de clientes com base em certificados

A autenticação baseada em certificados é por predefinição. Pode optar por desativar a autenticação baseada em certificados através do **inbound__clientAuth__clientCert__enabled**de propriedade. A tabela a seguir capta os possíveis valores.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Requer todos os pedidos no módulo 'Grade de Eventos' para apresentar um certificado de cliente. Além disso, terá de configurar **inbound__clientAuth__clientCert__source**.
| false | Não obrigue um cliente a apresentar o certificado.

A tabela seguinte capta os possíveis valores para **inbound__clientAuth__clientCert__source**

| Valores possíveis | Descrição |
| ---------------- | ------------ |
| IoT Edge | Predefinição. Utiliza o Trustbundle IoT Edge para validar todos os certificados de cliente.

Se um cliente apresentar um auto-assinado, por padrão, o módulo 'Grade de Eventos' rejeitará tais pedidos. Pode optar por permitir certificados de cliente auto-assinados através **de inbound__clientAuth__clientCert__allowUnknownCA** propriedade. A tabela a seguir capta os possíveis valores.

| Valores possíveis | Descrição |
| ----------------  | ------------|
| true | Predefinição. Permite que os certificados auto-assinados sejam apresentados com sucesso.
| false | Falhará os pedidos se forem apresentados certificados auto-assinados.

>[!IMPORTANT]
>Em cenários de produção, pode querer definir **inbound__clientAuth__clientCert__allowUnknownCA** **falsas**.

### <a name="sas-key-based-client-authentication"></a>Autenticação de clientes baseado em chaves SAS

Além da autenticação baseada em certificados, o módulo De Grelha de Eventos também pode fazer a autenticação baseada em chave SAS. A tecla SAS é como um segredo configurado no módulo De Grelha de Evento que deve usar para validar todas as chamadas recebidas. Os clientes precisam de especificar o segredo na 'chave aeg-sas-key' do cabeçalho HTTP. O pedido será rejeitado se `UnAuthorized` não corresponder.

A configuração para controlar a autenticação baseada em chaves SAS é **inbound__clientAuth__sasKeys__enabled**.

| Valores possíveis | Descrição  |
| ----------------  | ------------ |
| true | Permite a autenticação baseada em chaves SAS. Requer **inbound__clientAuth__sasKeys__key1** ou **inbound__clientAuth__sasKeys__key2**
| false | Predefinição. A autenticação baseada em chave SAS está desativada.

 **inbound__clientAuth__sasKeys__key1** e **inbound__clientAuth__sasKeys__key2** são as chaves que configura o módulo 'Grade de Eventos' para verificar contra os pedidos de entrada. Pelo menos uma das chaves tem de ser configurada. O cliente que esclarou o pedido terá de apresentar a chave como parte do cabeçalho do pedido '**aeg-sas-key**'. Se ambas as chaves estiverem configuradas, o cliente pode apresentar uma das chaves.

> [!NOTE]
>Pode configurar ambos os métodos de autenticação. Neste caso, a chave SAS é verificada primeiro e só se isso falhar, a autenticação baseada em certificados é realizada. Para que um pedido seja bem sucedido, apenas um dos métodos de autenticação precisa de ser bem sucedido.

## <a name="outbound-client-authentication"></a>Autenticação de clientes de saída

O cliente em contexto de saída refere-se ao módulo De Grelha de Eventos. A operação que está a ser feita está a entregar eventos aos assinantes. Os módulos de subscrição são considerados como o servidor.

Cada módulo IoT Edge é atribuído um Certificado de Identidade pelo daemon de segurança do IoT Edge. Usamos o certificado de identidade para chamadas de saída. Ao expirar, o módulo atualiza-se automaticamente com um novo certificado do daemon de segurança IoT Edge.

A configuração para controlar a autenticação do cliente de saída é **outbound__clientAuth__clientCert__enabled**.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Requer que todos os pedidos de saída do módulo Event Grid apresentem um certificado. Precisa de configurar **outbound__clientAuth__clientCert__source.**
| false | Não requer que o módulo De Grelha de Eventos apresente o seu certificado.

A configuração que controla a fonte do certificado é **outbound__clientAuth__clientCert__source**.

| Valores possíveis | Descrição |
| ---------------- | ------------ |
| IoT Edge | Predefinição. Utiliza o certificado de identidade do módulo configurado pelo daemon de segurança IoT Edge.

### <a name="outbound-server-authentication"></a>Autenticação do servidor de saída

Um dos tipos de destino para um assinante da Grelha de Eventos é "Webhook". Por padrão, apenas os pontos finais HTTPS são aceites para estes subscritores.

A configuração para controlar a política de destino webhook **outbound__webhook__httpsOnly**.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Permite apenas assinantes com ponto final HTTPS.
| false | Permite aos assinantes o ponto final HTTP ou HTTPS.

Por predefinição, o módulo 'Grade de Evento' validará o certificado de servidor do assinante. Pode saltar a validação sobressaíndo **outbound__webhook__skipServerCertValidation**. Os valores possíveis são:

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Não valide o certificado de servidor do assinante.
| false | Predefinição. Validar o certificado de servidor do assinante.

Se o certificado do assinante for auto-assinado, então por defeito o módulo De Grelha de Eventos rejeitará tais subscritores. Para permitir o certificado auto-assinado, pode anular **outbound__webhook__allowUnknownCA**. A tabela seguinte capta os valores possíveis.

| Valores possíveis | Descrição |
| ----------------  | ------------ |
| true | Predefinição. Permite que os certificados auto-assinados sejam apresentados com sucesso.
| false | Falhará os pedidos se forem apresentados certificados auto-assinados.

>[!IMPORTANT]
>Em cenários de produção, vai querer definir **outbound__webhook__allowUnknownCA** **falsas**.

> [!NOTE]
>O ambiente IoT Edge gera certificados auto-assinados. Recomendação é gerar certificados emitidos por AA autorizados para cargas de trabalho de produção e definir **propriedades AnknownCA** tanto na entrada como na saída para **falso**.

## <a name="summary"></a>Resumo

Um módulo de Grade de Eventos é **seguro por predefinição**. Recomendamos manter estes incumprimentos para as suas implementações de produção.

Seguem-se os princípios orientadores a utilizar enquanto configura:

* Permitir apenas pedidos HTTPS no módulo.
* Permitir apenas a autenticação do cliente baseada em certificados. Permitir apenas os certificados emitidos por AA bem conhecidos. Não permitir certificados auto-assinados.
* Não permitir a autenticação do cliente baseado no SASKey.
* Apresente sempre o certificado de identidade do módulo Event Grid em chamadas de saída.
* Deixe apenas os assinantes HTTPS para os tipos de destino Webhook.
* Valide sempre o certificado de servidor do assinante para os tipos de destino Webhook. Permitir apenas certificados emitidos por CAs bem conhecidos. Não permitir certificados auto-assinados.

Por predefinição, o módulo 'Grade de Evento' é implantado com a seguinte configuração:

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
