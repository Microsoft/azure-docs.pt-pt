---
title: 'Tutorial: Integre o sign-on single do Azure Ative Directory (SSO) com o Conector SAML Connector | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 19f6b0601afe9ad84f02c93d7f6e1ae3a71a06a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585099"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Integre o único sign-on da Azure AD com o Conector SAML, orquestrador de identidade da Maverics

Strata's Maverics Identity Orchestrator fornece uma forma simples de integrar aplicações no local com Azure Ative Directory (Azure AD) para autenticação e controlo de acessos. O Orquestrador Maverics é capaz de modernizar a autenticação e autorização para aplicações que atualmente dependem de cabeçalhos, cookies e outros métodos de autenticação proprietário. As instâncias do Orquestrador Maverics podem ser implantadas no local ou na nuvem. 

Este tutorial de acesso híbrido demonstra como migrar uma aplicação web no local que está atualmente protegida por um produto de gestão de acesso web legado para usar Azure AD para autenticação e controlo de acesso. Aqui estão os passos básicos:

1. Montar o Orquestrador Maverics
1. Proxy uma aplicação
1. Registar um pedido de empresa no Azure AD
1. Autenticar via Azure e autorizar o acesso à aplicação
1. Adicione cabeçalhos para acesso sem emenda à aplicação
1. Trabalhar com múltiplas aplicações

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada pelo Maverics Identity Orchestrator SAML Connector SSO. Para obter o software Maverics, contacte [as vendas da Strata.](mailto:sales@strata.io)
* Pelo menos uma aplicação que utiliza a autenticação baseada em cabeçalho. Os exemplos funcionam contra uma aplicação chamada Sonar, que está hospedada em https://app.sonarsystems.com , e uma aplicação chamada Connectulum, hospedada em https://app.connectulum.com .
* Uma máquina Linux para acolher o Orquestrador Maverics
  * OS: RHEL 7.7 ou superior, CentOS 7+
  * Disco: >= 10 GB
  * Memória: >= 4 GB
  * Portas: 22 (SSH/SCP), 443, 7474
  * Acesso à raiz para tarefas de instalação/administrativa
  * Saída de rede do servidor que hospeda o Orquestrador de Identidade Maverics para a sua aplicação protegida

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Passo 1: Montar o Orquestrador Maverics

### <a name="install-maverics"></a>Instalar Maverics

1. Obtenha o mais recente Maverics RPM. Copie a embalagem para o sistema em que pretende instalar o software Maverics.

1. Instale o pacote Maverics, substituindo o nome do seu ficheiro no lugar de `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Depois de instalar maverics, funcionará como um serviço sob `systemd` . Para verificar se o serviço está em execução, execute o seguinte comando:

   `sudo systemctl status maverics`

1. Para reiniciar o Orquestrador e seguir os registos, pode executar o seguinte comando:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Depois de instalar maverics, o ficheiro predefinido `maverics.yaml` é criado no `/etc/maverics` diretório. Antes de editar a sua configuração para incluir `appgateways` `connectors` e, o seu ficheiro de configuração será semelhante a este z:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Configurar o DNS

O DNS será útil para que não tenha de se lembrar do IP do servidor do Orquestrador.

Editar o ficheiro de anfitriões da máquina de navegador (do seu portátil), utilizando um hipotético IP orquestrador de 12.34.56.78. Nos sistemas operativos baseados em Linux, este ficheiro está localizado em `/etc/hosts` . No Windows, está localizado em `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Para confirmar que o DNS está configurado como esperado, pode fazer um pedido ao ponto final do Orquestrador. A partir do seu navegador, http://sonar.maverics.com:7474/status solicite.

### <a name="configure-tls"></a>Configurar TLS

Comunicar através de canais seguros para falar com o seu Orquestrador é fundamental para manter a segurança. Pode adicionar um par de certificado/chave na sua `tls` secção para o conseguir.

Para gerar um certificado e chave auto-assinados para o servidor Orchestrator, executar o seguinte comando a partir do `/etc/maverics` diretório:

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Para ambientes de produção, é provável que queira utilizar um certificado assinado por um conhecido CA para evitar avisos no navegador. [O "Let's Encrypt"](https://letsencrypt.org/) é uma opção boa e gratuita se estiver à procura de um CA de confiança.

Agora, use o certificado e chave recém-gerado para o Orquestrador. O seu ficheiro config deve agora conter este código:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Para confirmar que o TLS está configurado como esperado, reinicie o serviço Maverics e faça um pedido ao ponto final de estado. A partir do seu navegador, https://sonar.maverics.com/status solicite.

## <a name="step-2-proxy-an-application"></a>Passo 2: Proxy uma aplicação

Em seguida, configurar a procuração básica no Orquestrador através da utilização `appgateways` . Este passo ajuda-o a validar que o Orquestrador tem a conectividade necessária à aplicação protegida.

O seu ficheiro config deve agora conter este código:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Para confirmar que a procuração está a funcionar como esperado, reinicie o serviço Maverics e faça um pedido à aplicação através do representante da Maverics. A partir do seu navegador, https://sonar.maverics.com solicite. Pode opcionalmente fazer um pedido a recursos específicos da aplicação, por exemplo, `https://sonar.maverics.com/RESOURCE` onde é um recurso de `RESOURCE` aplicação válido da app protegida a montante.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Passo 3: Registar um pedido de empresa no Azure AD

Agora, crie uma nova aplicação empresarial em Azure AD que será usada para autenticar utilizadores finais.

> [!NOTE]
> Quando utiliza funcionalidades Azure AD como Acesso Condicional, é importante criar uma aplicação empresarial por aplicação no local. Isto permite o acesso condicional por app, avaliação de risco por aplicação, permissões por aplicação atribuídas, e assim por diante. Geralmente, uma aplicação empresarial em Azure AD mapeia para um conector Azure em Maverics.

Para registar um pedido de empresa na Azure AD:

1. No seu inquilino AZure AD, vá às **aplicações da Enterprise** e, em seguida, selecione **Nova Aplicação**. Na galeria Azure AD, procure o **Maverics Identity Orchestrator SAML Connector** e, em seguida, selecione-o.

1. No painel Maverics Identity Orchestrator SAML Connector **Properties,** de  definição **de atribuição do utilizador necessária?**

1. No painel de **visão geral** do conector de identidade Maverics, selecione **Configurar um único sinal de inscrição** e, em seguida, selecione **SAML**.

1. No sinal baseado no painel de cores saml do Maverics Identity Orchestrator **SAML,** edite a **Configuração Básica SAML** selecionando o botão **Edit** (ícone de lápis).

   ![Screenshot do botão de edição "Configuração BÁSICA SAML".](common/edit-urls.png)

1. Introduza um **ID** de entidade de `https://sonar.maverics.com` . O ID da entidade deve ser único em todas as aplicações do arrendatário, e pode ser um valor arbitrário. Utilizará este valor quando definir o `samlEntityID` campo para o seu conector Azure na secção seguinte.

1. Introduza um URL de **resposta** de `https://sonar.maverics.com/acs` . Utilizará este valor quando definir o `samlConsumerServiceURL` campo para o seu conector Azure na secção seguinte.

1. Introduza um **sinal no URL** de `https://sonar.maverics.com/` . Este campo não será utilizado pela Maverics, mas é necessário em Azure AD para permitir aos utilizadores terem acesso à aplicação através do portal Azure AD My Apps.

1. Selecione **Guardar**.

1. Na secção **Certificado de Assinatura SAML,** selecione o botão **Copiar** para copiar o valor URL **da Federação de Metadados de Aplicação** e guarde-o para o seu computador.

   ![Screenshot do botão de cópia "Certificado de assinatura SAML".](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Passo 4: Autenticar via Azure e autorizar o acesso à aplicação

Em seguida, coloque a aplicação da empresa que acabou de criar para usar configurando o conector Azure em Maverics. Esta `connectors` configuração emparelhada com o `idps` bloco permite ao Orquestrador autenticar os utilizadores.

O seu ficheiro config deve agora conter o seguinte código. Certifique-se de que substitui `METADATA_URL` o valor URL da Federação de Metadados da App Federation do passo anterior.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Para confirmar que a autenticação está a funcionar como esperado, reinicie o serviço Maverics e faça um pedido a um recurso de aplicação através do representante da Maverics. Deve ser redirecionado para Azure para autenticação antes de aceder ao recurso.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Passo 5: Adicionar cabeçalhos para acesso sem emenda à aplicação

Ainda não estás a enviar cabeçalhos para a candidatura a montante. Vamos adicionar `headers` ao pedido à medida que passa pelo proxy Maverics para permitir que a aplicação a montante identifique o utilizador.

O seu ficheiro config deve agora conter este código:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Para confirmar que a autenticação está a funcionar como esperado, faça um pedido a um recurso de aplicação através do representante da Maverics. O pedido protegido deve agora receber cabeçalhos no pedido. 

Sinta-se à vontade para editar as teclas do cabeçalho se a sua aplicação esperar diferentes cabeçalhos. Todas as reclamações que chegam do Azure AD como parte do fluxo SAML estão disponíveis para usar em cabeçalhos. Por exemplo, pode incluir outro cabeçalho de `secondary_email: azureSonarApp.email` , onde está o nome do `azureSonarApp` conector e é uma `email` reclamação devolvida a Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Passo 6: Trabalhar com múltiplas aplicações

Vamos agora dar uma olhada no que é necessário para procurar várias aplicações que estão em diferentes anfitriões. Para alcançar este passo, configura outra App Gateway, outra aplicação da empresa em Azure AD e outro conector.

O seu ficheiro config deve agora conter este código:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

Pode ter reparado que o código adiciona um `host` campo às definições de App Gateway. O `host` campo permite ao Orquestrador Maverics distinguir a montante o tráfego de procuração.

Para confirmar que a nova app Gateway está funcionando como esperado, faça um pedido para https://connectulum.maverics.com .

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="identity-migration"></a>Migração de identidade

Não suporta a sua ferramenta de gestão de acesso à Web em fim de vida, mas não tem como migrar os seus utilizadores sem resets de senha em massa? O Orquestrador Maverics apoia a migração de identidade através da utilização `migrationgateways` .

### <a name="web-server-gateways"></a>Gateways de servidor web

Não quer refazer a sua rede e o tráfego de procuração através do Orquestrador Maverics? Sem problema. O Orquestrador Maverics pode ser emparelhado com portais de servidor web (módulos) para oferecer as mesmas soluções sem procuração.

## <a name="wrap-up"></a>Embrulho

Neste momento, instalou o Orquestrador Maverics, criou e configura uma aplicação empresarial em Azure AD, e configura o Orquestrador para recorrer a uma aplicação protegida, exigindo a autenticação e a aplicação da política. Para saber mais sobre como o Orquestrador Maverics pode ser usado para casos de gestão de identidade distribuídos, [contacte Strata](mailto:sales@strata.io).

## <a name="next-steps"></a>Passos seguintes

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
