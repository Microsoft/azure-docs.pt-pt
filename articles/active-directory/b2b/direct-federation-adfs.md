---
title: Criar uma federação direta com um AD FS para B2B - Azure AD
description: Saiba como configurar a AD FS como fornecedor de identidade para a federação direta para que os hóspedes possam iniciar sessão nas suas aplicações Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272835"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Exemplo: Federação Direta com Serviços da Federação de Diretório Ativo (AD FS) (pré-visualização)
|     |
| --- |
| A federação direta é uma característica pública de pré-visualização do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como criar uma [federação direta](direct-federation.md) utilizando serviços da Federação de Diretórios Ativos (AD FS) como um fornecedor de identidade SAML 2.0 ou WS-Fed. Para apoiar a federação direta, certos atributos e reivindicações devem ser configurados no fornecedor de identidade. Para ilustrar como configurar um fornecedor de identidade para a federação direta, usaremos como exemplo os Serviços da Federação de Diretórios Ativos (AD FS). Vamos mostrar como criar a AD FS tanto como fornecedor de identidade SAML como como fornecedor de identidade WS-Fed.

> [!NOTE]
> Este artigo descreve como configurar AD FS tanto para saml como wS-Fed para fins de ilustração. Para integrações diretas da federação onde o fornecedor de identidade é AD FS, recomendamos a utilização da WS-Fed como protocolo. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Configure AD FS para a federação direta SAML 2.0
O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo SAML com requisitos específicos listados abaixo. Para ilustrar os passos de configuração do SAML, esta secção mostra como configurar AD FS para SAML 2.0. 

Para criar uma federação direta, os seguintes atributos devem ser recebidos na resposta SAML 2.0 do fornecedor de identidade. Estes atributos podem ser configurados ligando-se ao ficheiro XML do serviço de segurança online ou inserindo-os manualmente. Passo 12 em [Criar um teste AD FS exemplo](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como encontrar os pontos finais `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`AD FS ou como gerar o seu URL de metadados, por exemplo . 

|Atributo  |Valor  |
|---------|---------|
|Serviço de Consumidores de Afirmação     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emitente URI do parceiro IDP, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |

As seguintes reclamações devem ser configuradas no token SAML 2.0 emitido pelo fornecedor de identidade:


|Atributo  |Valor  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|endereço de e-mail     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


A secção seguinte ilustra como configurar os atributos e reivindicações necessários usando AD FS como um exemplo de um fornecedor de identidade SAML 2.0.

### <a name="before-you-begin"></a>Antes de começar

Um servidor AD FS já deve ser configurado e funcionando antes de iniciar este procedimento. Para obter ajuda na configuração de um servidor AD FS, consulte [Criar um teste AD FS 3.0 numa máquina virtual Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Adicione a descrição da reclamação

1. No seu servidor AD FS, selecione **Tools** > **AD FS management**.
2. No painel de navegação, selecione Descrições de**reivindicação**de **serviço** > .
3. Em **ações,** **selecione Adicionar Descrição de Reclamação**.
4. Na janela Adicionar uma Descrição de **Reclamações,** especifique os seguintes valores:

   - **Nome do visor**: Identificador Persistente
   - **Identificador de reclamação:**`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Selecione a caixa de verificação para **Publicar esta descrição de reclamação em metadados da federação como um tipo**de reclamação que este serviço da federação pode aceitar .
   - Selecione a caixa de verificação para **Publicar esta descrição de reclamação nos metadados da federação como um tipo**de reclamação que este serviço da federação pode enviar .

5. Clique em **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicione as regras de confiança do partido e reivindicação

1. No servidor AD FS, vá à gestão de **AD** > **FS**tools.
2. No painel de navegação, selecione **Trust Relationships** > **Trusting Party Trusts**.
3. No âmbito **das ações,** selecione **Adicionar Confiança no Partido .** 
4. No add trust wizard para **Select Data Source,** utilize a opção **Dados de Importação sobre a parte que depende publicada online ou numa rede local**. Especifique este https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlURL de metadados da federação- . Deixe outras seleções padrão. Selecione **Fechar**.
5. O feiticeiro das Regras de Reclamação de **Edição** abre.
6. No assistente de regras de reclamação de **edição,** selecione **Adicionar Regra**. Em Escolher O Tipo de **Regra, selecione** **Enviar Atributos LDAP como Reclamações**. Selecione **Next**.
7. Na regra de **reclamação configurada,** especifique os seguintes valores: 

   - **Nome da regra da reclamação**: Regra de reclamação de e-mail 
   - **Loja de atributos**: Diretório Ativo 
   - **Atributo LDAP**: Endereços de e-mail 
   - Tipo de **reclamação de saída**: Endereço de e-mail

8. Selecione **Concluir**.
9. A janela Regras de Reclamação de **Edição** mostrará a nova regra. Clique em **Aplicar**. 
10. Clique em **OK**.  

### <a name="create-an-email-transform-rule"></a>Criar uma regra de transformação de e-mail
1. Vá para editar regras de **reclamação** e clique em **Adicionar Regra**. Em Escolher O Tipo de **Regra, selecione** **Transforme uma Reclamação de Entrada** e clique em **Next**. 
2. Na regra de **reclamação configurada,** especifique os seguintes valores: 

   - **Nome da regra da reclamação**: E-mail transformar regra 
   - Tipo de **reclamação de entrada**: Endereço de e-mail 
   - **Tipo de reclamação de saída**: ID de nome 
   - **Formato ID de nome de saída**: Identificador Persistente 
   - Selecione **Passar todos os valores de afirmação**.

3. Clique em **Concluir**. 
4. A janela Regras de Reclamação de **Edição** mostrará as novas regras. Clique em **Aplicar**. 
5. Clique em **OK**. O servidor AD FS está agora configurado para a federação direta utilizando o protocolo SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Configure AD FS para a federação direta da WS-Fed 
O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo WS-Fed com os requisitos específicos listados abaixo. Atualmente, os dois fornecedores da WS-Fed foram testados para compatibilidade com a Azure AD incluem AD FS e Shibboleth. Aqui, usaremos os Serviços da Federação de Diretórios Ativos (AD FS) como um exemplo do fornecedor de identidade WS-Fed. Para obter mais informações sobre o estabelecimento de uma confiança de parte dependente entre um fornecedor compatível com a WS-Fed com a Azure AD, descarregue o Azure AD Identity Provider Compatibility Docs.

Para criar uma federação direta, os seguintes atributos devem ser recebidos na mensagem WS-Fed do fornecedor de identidade. Estes atributos podem ser configurados ligando-se ao ficheiro XML do serviço de segurança online ou inserindo-os manualmente. Passo 12 em [Criar um teste AD FS exemplo](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como encontrar os pontos finais `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`AD FS ou como gerar o seu URL de metadados, por exemplo .
 
|Atributo  |Valor  |
|---------|---------|
|Ponto final do PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emitente URI do parceiro IDP, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |

Pedidos necessários para o token WS-Fed emitidos pelo IDP:

|Atributo  |Valor  |
|---------|---------|
|Id imutável     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|endereço de e-mail     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

A secção seguinte ilustra como configurar os atributos e reivindicações necessários usando AD FS como um exemplo de um fornecedor de identidade WS-Fed.

### <a name="before-you-begin"></a>Antes de começar
Um servidor AD FS já deve ser configurado e funcionando antes de iniciar este procedimento. Para obter ajuda na configuração de um servidor AD FS, consulte [Criar um teste AD FS 3.0 numa máquina virtual Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicione as regras de confiança do partido e reivindicação 
1. No servidor AD FS, vá à gestão de **AD** > **FS**tools. 
1. No painel de navegação, selecione **Trust Relationships** > **Trusting Party Trusts**. 
1. No âmbito **das ações,** selecione **Adicionar Confiança no Partido .**  
1. No assistente fiduciário do grupo, para **Select Data Source,** utilize a opção **Dados de Importação sobre a parte que depende publicada online ou numa rede local**. Especifique este `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`URL de metadados da federação: .  Deixe outras seleções padrão. Selecione **Fechar**.
1. O feiticeiro das Regras de Reclamação de **Edição** abre. 
1. No assistente de regras de reclamação de **edição,** selecione **Adicionar Regra**. Em Escolha O Tipo de **Regra, selecione** **Enviar Reclamações usando uma regra personalizada**. Selecione *Next*. 
1. Na regra de **reclamação configurada,** especifique os seguintes valores:

   - **Nome da regra da reclamação**: Emissão Id imutável  
   - **Regra personalizada:**`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selecione **Concluir**. 
1. A janela Regras de Reclamação de **Edição** mostrará a nova regra. Clique em **Aplicar**.  
1. No mesmo assistente de regras de reclamação de **edição,** selecione **Adicionar Regra**. No Tipo de Regra da **Cohose,** selecione **Enviar Atributos LDAP como Reclamações**. Selecione **Next**.
1. Na regra de **reclamação configurada,** especifique os seguintes valores: 

   - **Nome da regra da reclamação**: Regra de reclamação de e-mail  
   - **Loja de atributos**: Diretório Ativo  
   - **Atributo LDAP**: Endereços de e-mail  
   - Tipo de **reclamação de saída**: Endereço de e-mail 

1.  Selecione **Concluir**. 
1.  A janela Regras de Reclamação de **Edição** mostrará a nova regra. Clique em **Aplicar**.  
1.  Clique em **OK**. O servidor AD FS está agora configurado para a federação direta usando wS-Fed.

## <a name="next-steps"></a>Passos seguintes
Em seguida, configurará a [federação direta em Azure AD,](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) quer no portal Azure AD, quer através da utilização do PowerShell. 
