---
title: Azure AD Connect - Atualizar o certificado TLS/SSL para uma exploração ad FS [ Microsoft Docs
description: Este documento detalha as etapas para atualizar o certificado TLS/SSL de uma exploração AD FS utilizando o Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cc768162d98402fe52b52b2826a9dbf2840a581
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80331744"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Atualizar o certificado TLS/SSL para uma exploração de Serviços da Federação de Diretórios Ativos (AD FS)

## <a name="overview"></a>Descrição geral
Este artigo descreve como pode utilizar o Azure AD Connect para atualizar o certificado TLS/SSL para uma exploração de Serviços da Federação de Diretórios Ativos (AD FS). Pode utilizar a ferramenta Azure AD Connect para atualizar facilmente o certificado TLS/SSL para a exploração AD FS, mesmo que o método de entrada do utilizador selecionado não seja AD FS.

Pode executar toda a operação de atualização do certificado TLS/SSL para a exploração AD FS em todos os servidores da Federação e Da Aplicação Web Proxy (WAP) em três passos simples:

![Três passos](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Para saber mais sobre os certificados utilizados pela AD FS, consulte [os certificados de compreensão utilizados pela AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Pré-requisitos

* **AD FS Farm**: Certifique-se de que a sua quinta AD FS é baseada no Windows Server 2012 R2 ou posteriormente.
* **Azure AD Connect**: Certifique-se de que a versão do Azure AD Connect é de 1.1.553.0 ou superior. Utilizará o **certificado AD FS SSL**da atualização de tarefas .

![Atualizar tarefa TLS](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Passo 1: Fornecer informações agrícolas aD FS

A Azure AD Connect tenta obter automaticamente informações sobre a exploração aD FS:
1. Consultando as informações da quinta a partir de AD FS (Windows Server 2016 ou posterior).
2. Referenciando as informações de execuções anteriores, que são armazenadas localmente com o Azure AD Connect.

Pode modificar a lista de servidores que são apresentados adicionando ou removendo os servidores para refletir a configuração atual da quinta AD FS. Assim que as informações do servidor são fornecidas, o Azure AD Connect apresenta a conectividade e o estado atual do certificado TLS/SSL.

![Informações do servidor AD FS](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Se a lista contiver um servidor que já não faça parte da exploração AD FS, clique em **Remover** para eliminar o servidor da lista de servidores na sua exploração AD FS.

![Servidor offline na lista](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Remover um servidor da lista de servidores para uma exploração AD FS em Azure AD Connect é uma operação local e atualiza as informações para a fazenda AD FS que o Azure AD Connect mantém localmente. O Azure AD Connect não modifica a configuração do AD FS para refletir a alteração.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Passo 2: Fornecer um novo certificado TLS/SSL

Depois de ter confirmado as informações sobre servidores agrícolas AD FS, o Azure AD Connect pede o novo certificado TLS/SSL. Forneça um certificado PFX protegido por palavra-passe para continuar a instalação.

![Certificado TLS/SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Depois de fornecer o certificado, o Azure AD Connect passa por uma série de pré-requisitos. Verifique o certificado para garantir que o certificado está correto para a exploração aD FS:

-   O nome do assunto/nome suplente para o certificado é o mesmo que o nome do serviço da federação, ou é um certificado wildcard.
-   O certificado é válido por mais de 30 dias.
-   A cadeia de fidedignidade de certificados é válida.
-   O certificado está protegido por palavra-passe.

## <a name="step-3-select-servers-for-the-update"></a>Passo 3: Selecione servidores para a atualização

No próximo passo, selecione os servidores que precisam de ter o certificado TLS/SSL atualizado. Os servidores offline não podem ser selecionados para a atualização.

![Selecione servidores para atualizar](./media/how-to-connect-fed-ssl-update/selectservers.png)

Depois de completar a configuração, o Azure AD Connect apresenta a mensagem que indica o estado da atualização e fornece uma opção para verificar o início de entrada do AD FS.

![Configuração completa](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>FAQs

* **Qual deve ser o nome do certificado para o novo certificado AD FS TLS/SSL?**

    A Azure AD Connect verifica se o nome/nome do assunto alternativo do certificado contém o nome do serviço da federação. Por exemplo, se o seu nome de serviço da federação for fs.contoso.com, o nome do assunto/nome alternativo deve ser fs.contoso.com.  Os certificados Wildcard também são aceites.

* **Por que me pedem credenciais novamente na página do servidor WAP?**

    Se as credenciais que fornece para a ligação aos servidores AD FS também não têm o privilégio de gerir os servidores WAP, então o Azure AD Connect pede credenciais que tenham privilégio administrativo nos servidores WAP.

* **O servidor é mostrado como offline. O que devo fazer?**

    O Azure AD Connect não pode executar qualquer operação se o servidor estiver offline. Se o servidor fizer parte da exploração AD FS, verifique a conectividade com o servidor. Depois de resolver o problema, prima o ícone de atualização para atualizar o estado do assistente. Se o servidor fez parte da quinta mais cedo mas agora já não existe, clique em **Remover** para eliminá-lo da lista de servidores que o Azure AD Connect mantém. Remover o servidor da lista no Azure AD Connect não altera a configuração AD FS em si. Se estiver a utilizar AD FS no Windows Server 2016 ou mais tarde, o servidor permanece nas definições de configuração e será mostrado novamente da próxima vez que a tarefa for executada.

* **Posso atualizar um subconjunto dos meus servidores agrícolas com o novo certificado TLS/SSL?**

    Sim. Pode sempre executar novamente o **Certificado SSL** de atualização de tarefas para atualizar os restantes servidores. Nos servidores Select para a página de atualização de **certificados SSL,** pode classificar a lista de servidores na **data De Validade Do SSL** para aceder facilmente aos servidores que ainda não estão atualizados.

* **Removi o servidor na execução anterior, mas ainda está a ser mostrado como offline e listado na página aD FS Servers. Porque é que o servidor offline ainda lá está, mesmo depois de o ter removido?**

    Remover o servidor da lista no Azure AD Connect não o remove na configuração AD FS. Azure AD Connect refere AD FS (Windows Server 2016 ou superior) para qualquer informação sobre a quinta. Se o servidor ainda estiver presente na configuração AD FS, será listado novamente na lista.  

## <a name="next-steps"></a>Passos seguintes

- [Azure AD Connect e federação](how-to-connect-fed-whatis.md)
- [Gestão e personalização de serviços da Federação de Diretórios Ativos com Azure AD Connect](how-to-connect-fed-management.md)

