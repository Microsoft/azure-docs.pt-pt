---
title: Criar federação direta com um AD FS para B2B - Azure AD
description: Saiba como configurar o AD FS como fornecedor de identidade para federação direta para que os hóspedes possam iniciar seducação nas suas aplicações AD AZure
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3d7c47ff0a2c533bf12a67958a913b22915f75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909565"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Exemplo: Federação direta com Serviços da Federação de Diretórios Ativos (AD FS) (pré-visualização)

> [!NOTE]
> A federação direta é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve como criar uma [federação direta](direct-federation.md) utilizando os Serviços da Federação de Diretório Ativo (AD FS) como um SAML 2.0 ou WS-Fed fornecedor de identidade. Para apoiar a federação direta, certos atributos e reclamações devem ser configurados no fornecedor de identidade. Para ilustrar como configurar um fornecedor de identidade para a federação direta, usaremos os Serviços da Federação de Diretórios Ativos (AD FS) como exemplo. Vamos mostrar como configurar o AD FS tanto como fornecedor de identidade SAML como como um fornecedor de identidade WS-Fed.

> [!NOTE]
> Este artigo descreve como configurar a AD FS tanto para SAML como WS-Fed para fins de ilustração. Para integrações diretas da federação em que o fornecedor de identidade é AD FS, recomendamos a utilização WS-Fed como protocolo. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Configure AD FS para federação direta SAML 2.0
O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo SAML com requisitos específicos listados abaixo. Para ilustrar os passos de configuração DO SAML, esta secção mostra como configurar o AD FS para SAML 2.0. 

Para criar uma federação direta, os seguintes atributos devem ser recebidos na resposta SAML 2.0 do fornecedor de identidade. Estes atributos podem ser configurados ligando-os ao ficheiro XML do serviço de segurança online ou introduzindo-os manualmente. Passo 12 na [Criação de um teste AD FS exemplos](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) como encontrar os pontos finais AD FS ou como gerar o seu URL de metadados, por exemplo `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` . 

|Atributo  |Valor  |
|---------|---------|
|Serviço De Afirmação     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emitente URI do IdP parceiro, por exemplo `http://www.example.com/exk10l6w90DHM0yi...`         |

As seguintes alegações devem ser configuradas no token SAML 2.0 emitido pelo fornecedor de identidade:


|Atributo  |Valor  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|endereço de e-mail     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


A secção seguinte ilustra como configurar os atributos e alegações necessários usando AD FS como exemplo de um fornecedor de identidade SAML 2.0.

### <a name="before-you-begin"></a>Antes de começar

Um servidor AD FS já deve ser configurado e funcionando antes de iniciar este procedimento. Para obter ajuda na configuração de um servidor AD FS, consulte [Criar uma instância AD FS 3.0 de teste numa máquina virtual Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Adicione a descrição da reclamação

1. No seu servidor AD FS, selecione **Tools**  >  **AD FS management**.
2. No painel de navegação, selecione **Service**  >  **Descrições de Reclamações de**Serviço .
3. Em **Ações**, **selecione Adicionar Descrição de Reclamações**.
4. Na janela **Adicionar uma Descrição de Reclamação,** especifique os seguintes valores:

   - **Nome do visor**: Identificador Persistente
   - **Identificador de reclamação:**`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Selecione a caixa de verificação para **Publicar esta descrição de reclamação nos metadados da federação como um tipo de reclamação que este serviço da federação pode aceitar**.
   - Selecione a caixa de verificação para **Publicar esta descrição de reclamação nos metadados da federação como um tipo de reclamação que este serviço de federação pode enviar**.

5. Clique em **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicione a confiança do partido confiando e reivindicando regras

1. No servidor AD FS, **Tools**aceda à  >  **gestão de FS AD ferramentas**.
2. No painel de navegação, selecione **Trust Relationships**  >  **Relying Party Trusts**.
3. Under **Actions**, selecione **Add Relying Party Trust**. 
4. No adding relying party trust wizard for **Select Data Source**, use a opção Importar **dados sobre a parte que conta publicada online ou numa rede local**. Especifique este URL de metadados da https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml federação. Deixe outras seleções predefinidos. Selecione **Fechar**.
5. O assistente **de Regras de Reclamação de Edição** abre.
6. No assistente **de Regras de Reclamação de Edição,** selecione **Adicionar Regra**. No **Tipo de Regra de Escolha,** selecione Enviar **Atributos LDAP como Reclamações**. Selecione **Seguinte**.
7. Na **Regra de Reclamação DeConfigure,** especifique os seguintes valores: 

   - **Nome da regra da reclamação**: Regra da reclamação de e-mail 
   - **Loja de atributos**: Diretório Ativo 
   - **Atributo LDAP**: Endereços de e-mail 
   - **Tipo de reclamação de saída**: Endereço de e-mail

8. Selecione **Concluir**.
9. A janela **'Regras de Reclamação de Edição'** apresentará a nova regra. Clique em **Aplicar**. 
10. Clique em **OK**.  

### <a name="create-an-email-transform-rule"></a>Criar uma regra de transformação de e-mail
1. Vá para **editar as regras de reclamação** e clique em **Adicionar Regra**. In **Choose Rule Type**, selecione Transform a Incoming **Claim** and click **Next**. 
2. Na **Regra de Reclamação DeConfigure,** especifique os seguintes valores: 

   - **Nome da regra da reclamação**: Regra de transformação de e-mail 
   - **Tipo de reclamação recebida**: Endereço de e-mail 
   - **Tipo de reclamação de saída**: ID de nome 
   - **Formato iD de nome de saída**: Identificador Persistente 
   - Selecione **Passar todos os valores de afirmação**.

3. Clique em **Concluir**. 
4. A janela **'Regras de Reclamação de Edição'** apresentará as novas regras. Clique em **Aplicar**. 
5. Clique em **OK**. O servidor AD FS está agora configurado para federação direta utilizando o protocolo SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Configure AD FS para WS-Fed federação direta 
O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizem o protocolo WS-Fed com os requisitos específicos listados abaixo. Atualmente, os dois fornecedores de WS-Fed foram testados para compatibilidade com a Azure AD incluem AD FS e Shibboleth. Aqui, usaremos os Serviços da Federação de Diretórios Ativos (AD FS) como um exemplo do provedor de identidade WS-Fed. Para obter mais informações sobre a criação de uma confiança entre uma parte WS-Fed fornecedor compatível com a Azure AD, descarregue os Docs de Compatibilidade do Fornecedor de Identidade Azure AD.

Para criar uma federação direta, os seguintes atributos devem ser recebidos na mensagem WS-Fed do fornecedor de identidade. Estes atributos podem ser configurados ligando-os ao ficheiro XML do serviço de segurança online ou introduzindo-os manualmente. Passo 12 na [Criação de um teste AD FS exemplos](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) como encontrar os pontos finais AD FS ou como gerar o seu URL de metadados, por exemplo `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` .
 
|Atributo  |Valor  |
|---------|---------|
|PassivoRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emitente URI do IdP parceiro, por exemplo `http://www.example.com/exk10l6w90DHM0yi...`         |

Reclamações necessárias para o WS-Fed ficha emitida pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|ImutávelID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|endereço de e-mail     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

A secção seguinte ilustra como configurar os atributos e alegações necessários usando AD FS como um exemplo de um fornecedor de identidade WS-Fed.

### <a name="before-you-begin"></a>Antes de começar
Um servidor AD FS já deve ser configurado e funcionando antes de iniciar este procedimento. Para obter ajuda na configuração de um servidor AD FS, consulte [Criar uma instância AD FS 3.0 de teste numa máquina virtual Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicione a confiança do partido confiando e reivindicando regras 
1. No servidor AD FS, **Tools**aceda à  >  **gestão de FS AD ferramentas**. 
1. No painel de navegação, selecione **Trust Relationships**  >  **Relying Party Trusts**. 
1. Under **Actions**, selecione **Add Relying Party Trust**.  
1. No assistente de confiança do partido, para **Select Data Source,** utilize a opção **Importar dados sobre a parte que conta publicada online ou numa rede local.** Especifique este URL de metadados da federação: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml` .  Deixe outras seleções predefinidos. Selecione **Fechar**.
1. O assistente **de Regras de Reclamação de Edição** abre. 
1. No assistente **de Regras de Reclamação de Edição,** selecione **Adicionar Regra**. No **Tipo de Regra de Escolha,** selecione Enviar **Reclamações Usando uma Regra Personalizada**. Selecione *Seguinte*. 
1. Na **Regra de Reclamação DeConfigure,** especifique os seguintes valores:

   - **Nome da regra da reclamação**: Id Imutável de emissão  
   - **Regra personalizada:**`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selecione **Concluir**. 
1. A janela **'Regras de Reclamação de Edição'** apresentará a nova regra. Clique em **Aplicar**.  
1. No mesmo assistente **de Regras de Reclamação de Edição,** selecione **Adicionar Regra**. No **Tipo de Regra de Cohose,** selecione Enviar **atributos LDAP como Reclamações**. Selecione **Seguinte**.
1. Na **Regra de Reclamação DeConfigure,** especifique os seguintes valores: 

   - **Nome da regra da reclamação**: Regra da reclamação de e-mail  
   - **Loja de atributos**: Diretório Ativo  
   - **Atributo LDAP**: Endereços de e-mail  
   - **Tipo de reclamação de saída**: Endereço de e-mail 

1.  Selecione **Concluir**. 
1.  A janela **'Regras de Reclamação de Edição'** apresentará a nova regra. Clique em **Aplicar**.  
1.  Clique em **OK**. O servidor AD FS está agora configurado para federação direta usando a WS-Fed.

## <a name="next-steps"></a>Passos seguintes
Em seguida, você configurará [a federação direta em Azure AD,](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) quer no portal AD AZure ou usando PowerShell. 
