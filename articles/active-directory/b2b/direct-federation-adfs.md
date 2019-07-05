---
title: Configurar a Federação direta com um AD FS para B2B - Azure Active Directory | Documentos da Microsoft
description: Saiba como configurar do AD FS como fornecedor de identidade para a Federação direto para que os convidados podem iniciar sessão às suas aplicações do Azure AD
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
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544325"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Exemplo: Federação direta com serviços de Federação do Active Directory (AD FS) (pré-visualização)
|     |
| --- |
| Federação direta é uma funcionalidade de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como configurar [direcionar o Federação](direct-federation.md) usando os serviços de Federação do Active Directory (AD FS) como um SAML 2.0 ou WS-Fed fornecedor de identidade. Para suportar o Federação direta, determinadas afirmações e atributos devem ser configuradas no fornecedor de identidade. Para ilustrar como configurar um fornecedor de identidade para a Federação direto, vamos utilizar serviços de Federação do Active Directory (AD FS) como exemplo. Vamos mostrar como configurar o AD FS como um fornecedor de identidade e como um fornecedor de identidade de WS-Fed.

> [!NOTE]
> Este artigo descreve como configurar o AD FS para SAML e WS-Fed para fins de ilustração. Para integrações de Federação direto em que o fornecedor de identidade é do AD FS, recomendamos que utilize o WS-Fed como o protocolo. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Configurar o AD FS para Federação direto de SAML 2.0
O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo SAML com requisitos específicos indicados abaixo. Para ilustrar os passos de configuração de SAML, esta secção mostra como configurar o AD FS para SAML 2.0. 

Para configurar a Federação direta, os seguintes atributos têm de ser recebidos na resposta SAML 2.0 de fornecedor de identidade. Estes atributos podem ser configurados ao criar uma ligação para o serviço de token de segurança online arquivo XML ou introduzindo-los manualmente. Passo 12 [criar uma instância de teste do AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como encontrar os pontos de extremidade do AD FS ou como gerar o URL de metadados, por exemplo `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`. 

|Atributo  |Value  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do parceiro IdP, por exemplo do emissor `http://www.example.com/exk10l6w90DHM0yi...`         |

As seguintes declarações tem de ser configuradas no token SAML 2.0 emitido pelo fornecedor de identidade:


|Atributo  |Value  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


A secção seguinte ilustra como configurar os atributos necessários e as afirmações do AD FS a utilizar como um exemplo de um fornecedor de identidade de SAML 2.0.

### <a name="before-you-begin"></a>Antes de começar

Um servidor do AD FS já tem de ser definido se e a funcionar antes de iniciar este procedimento. Para obter ajuda com a definição de segurança de um servidor do AD FS, consulte [criar uma instância do teste do AD FS 3.0 numa máquina virtual do Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Adicionar a descrição de afirmação

1. No servidor do AD FS, selecione **ferramentas** > **gestão do AD FS**.
2. No painel de navegação, selecione **serviço** > **descrições de afirmação**.
3. Sob **ações**, selecione **Adicionar descrição de afirmação**.
4. Na **adicione uma descrição de afirmação** janela, especifique os seguintes valores:

   - **Nome a apresentar**: Identificador persistente
   - **Identificador de afirmação**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Selecione a caixa de verificação **publicar esta descrição de afirmação nos metadados da federação como tipo de afirmação que este serviço de Federação pode aceitar**.
   - Selecione a caixa de verificação **publicar esta descrição de afirmação nos metadados da federação como tipo de afirmação que este serviço de Federação pode enviar**.

5. Clique em **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicionar a confiança de entidade confiadora e as regras de afirmação

1. No servidor do AD FS, aceda a **ferramentas** > **gestão do AD FS**.
2. No painel de navegação, selecione **relações de confiança** > **entidade Confiadora confianças**.
3. Sob **ações**, selecione **adicionar entidade Confiadora**. 
4. No Assistente Adicionar confiança de entidade confiadora para confiança terceiros para **selecionar origem de dados**, utilize a opção **importar dados sobre a entidade confiadora publicados online ou numa rede local**. Especificar estes metadados de Federação URL - https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml. Deixe as outras predefinições. Selecione **fechar**.
5. O **editar regras de afirmação** é aberto o assistente.
6. Na **editar regras de afirmação** assistente, selecione **Adicionar regra**. Na **escolha o tipo de regra**, selecione **enviar atributos LDAP como afirmações**. Selecione **Seguinte**.
7. Na **configurar regra de afirmação**, especifique os seguintes valores: 

   - **Nome da regra de afirmação**: Regra de afirmação de e-mail 
   - **Arquivo de atributos**: Active Directory 
   - **O atributo LDAP**: Endereços do correio electrónico 
   - **Tipo de afirmação de saída**: Endereço de email

8. Selecione **Concluir**.
9. O **editar regras de afirmação** janela mostrará a nova regra. Clique em **Aplicar**. 
10. Clique em **OK**.  

### <a name="create-an-email-transform-rule"></a>Criar uma regra de transformação de e-mail
1. Aceda a **editar regras de afirmação** e clique em **Adicionar regra**. Na **escolha o tipo de regra**, selecione **transformar uma afirmação de entrada** e clique em **seguinte**. 
2. Na **configurar regra de afirmação**, especifique os seguintes valores: 

   - **Nome da regra de afirmação**: Regras de transformação de e-mail 
   - **Tipo de afirmação de entrada**: Endereço de email 
   - **Tipo de afirmação de saída**: ID de nome 
   - **Formato de ID de nome saído**: Identificador persistente 
   - Selecione **Passar todos os valores de afirmação**.

3. Clique em **Concluir**. 
4. O **editar regras de afirmação** janela apresentará as novas regras. Clique em **Aplicar**. 
5. Clique em **OK**. Servidor do AD FS está agora configurado para Federação direta usando o protocolo SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Configurar o AD FS para Federação direto de WS-Fed 
O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo WS-Fed com os requisitos específicos indicados abaixo. Atualmente, os dois provedores de WS-Fed foram testados para compatibilidade com o Azure AD incluem o AD FS e do Shibboleth. Aqui, vamos utilizar serviços de Federação do Active Directory (AD FS) como um exemplo do fornecedor de identidade de WS-Fed. Para obter mais informações sobre como estabelecer uma fidedignidade de entidade confiadora entre um fornecedor de WS-Fed em conformidade com o Azure AD, transferir os documentos de compatibilidade do fornecedor de identidade do Azure AD.

Para configurar a Federação direta, os seguintes atributos têm de ser recebidos na mensagem de WS-Fed do fornecedor de identidade. Estes atributos podem ser configurados ao criar uma ligação para o serviço de token de segurança online arquivo XML ou introduzindo-los manualmente. Passo 12 [criar uma instância de teste do AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como encontrar os pontos de extremidade do AD FS ou como gerar o URL de metadados, por exemplo `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`.
 
|Atributo  |Value  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do parceiro IdP, por exemplo do emissor `http://www.example.com/exk10l6w90DHM0yi...`         |

Declarações necessárias para o token de WS-Fed emitido o IDP utiliza:

|Atributo  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

A secção seguinte ilustra como configurar os atributos necessários e as afirmações do AD FS a utilizar como um exemplo de um fornecedor de identidade de WS-Fed.

### <a name="before-you-begin"></a>Antes de começar
Um servidor do AD FS já tem de ser definido se e a funcionar antes de iniciar este procedimento. Para obter ajuda com a definição de segurança de um servidor do AD FS, consulte [criar uma instância do teste do AD FS 3.0 numa máquina virtual do Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicionar a confiança de entidade confiadora e as regras de afirmação 
1. No servidor do AD FS, aceda a **ferramentas** > **gestão do AD FS**. 
1. No painel de navegação, selecione **relações de confiança** > **entidade Confiadora confianças**. 
1. Sob **ações**, selecione **adicionar entidade Confiadora**.  
1. No Assistente de confiança de terceiros, de entidade confiadora para adicionar **selecionar origem de dados**, utilize a opção **importar dados sobre a entidade confiadora publicados online ou numa rede local**. Especifique este URL de metadados de Federação: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`.  Deixe as outras predefinições. Selecione **fechar**.
1. O **editar regras de afirmação** é aberto o assistente. 
1. Na **editar regras de afirmação** assistente, selecione **Adicionar regra**. Na **escolha o tipo de regra**, selecione **enviar afirmações utilizando uma regra personalizada**. Selecione *Seguinte*. 
1. Na **configurar regra de afirmação**, especifique os seguintes valores:

   - **Nome da regra de afirmação**: Id imutável do problema  
   - **Regra personalizada**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selecione **Concluir**. 
1. O **editar regras de afirmação** janela mostrará a nova regra. Clique em **Aplicar**.  
1. Da mesma **editar regras de afirmação** assistente, selecione **Adicionar regra**. Na **tipo de regra Cohose**, selecione **enviar atributos LDAP como afirmações**. Selecione **Seguinte**.
1. Na **configurar regra de afirmação**, especifique os seguintes valores: 

   - **Nome da regra de afirmação**: Regra de afirmação de e-mail  
   - **Arquivo de atributos**: Active Directory  
   - **O atributo LDAP**: Endereços do correio electrónico  
   - **Tipo de afirmação de saída**: Endereço de email 

1.  Selecione **Concluir**. 
1.  O **editar regras de afirmação** janela mostrará a nova regra. Clique em **Aplicar**.  
1.  Clique em **OK**. Servidor do AD FS está agora configurado para Federação direta usando o WS-Fed.

## <a name="next-steps"></a>Passos Seguintes
Em seguida, vai [configurar a Federação direta no Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) no portal do Azure AD ou com o PowerShell. 
