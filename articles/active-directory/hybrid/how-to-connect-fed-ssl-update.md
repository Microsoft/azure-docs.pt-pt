---
title: Azure AD Connect - Atualize o certificado TLS/SSL para uma exploração AD FS | Microsoft Docs
description: Este documento detalha os passos para atualizar o certificado TLS/SSL de uma exploração AD FS utilizando o Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451b50e70b98849dfc4654566d09a5a961abe451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279912"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Atualizar o certificado TLS/SSL para uma exploração de Serviços da Federação de Diretórios Ativos (AD FS)

## <a name="overview"></a>Descrição Geral
Este artigo descreve como pode utilizar o Azure AD Connect para atualizar o certificado TLS/SSL para uma exploração de Serviços da Federação de Diretórios Ativos (AD FS). Pode utilizar a ferramenta Azure AD Connect para atualizar facilmente o certificado TLS/SSL para a exploração AD FS, mesmo que o método de entrada do utilizador selecionado não seja AD FS.

Pode executar toda a operação de atualização do certificado TLS/SSL para a fazenda AD FS em todos os servidores da Federação e aplicação web Proxy (WAP) em três passos simples:

![Três passos](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Para saber mais sobre certificados utilizados pela AD FS, consulte [certificados de compreensão utilizados pela AD FS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc730660(v=ws.11)).

## <a name="prerequisites"></a>Pré-requisitos

* **AD FS Farm**: Certifique-se de que a sua quinta AD FS é baseada no Windows Server 2012 ou mais tarde.
* **Ligação AD AD:** Certifique-se de que a versão do Azure AD Connect é 1.1.553.0 ou superior. Utilizará o **certificado AD FS SSL de atualização** de tarefas .

![Atualizar tarefa TLS](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Passo 1: Fornecer informações sobre a exploração da AD FS

A azure AD Connect tenta obter informações sobre a exploração AD FS automaticamente:
1. Consultando as informações da fazenda a partir de AD FS (Windows Server 2016 ou posterior).
2. Referenciando as informações de execuções anteriores, que são armazenadas localmente com Azure AD Connect.

Pode modificar a lista de servidores que são apresentados adicionando ou removendo os servidores para refletir a configuração atual da fazenda AD FS. Assim que a informação do servidor é fornecida, o Azure AD Connect exibe a conectividade e o estado atual do certificado TLS/SSL.

![Informações do servidor AD FS](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Se a lista contiver um servidor que já não faz parte da fazenda AD FS, clique em **Remover** para eliminar o servidor da lista de servidores na sua fazenda AD FS.

![Servidor offline na lista](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Remover um servidor da lista de servidores de uma fazenda AD FS em Azure AD Connect é uma operação local e atualiza as informações para a fazenda AD FS que o Azure AD Connect mantém localmente. O Azure AD Connect não modifica a configuração em AD FS para refletir a mudança.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Passo 2: Fornecer um novo certificado TLS/SSL

Depois de ter confirmado as informações sobre os servidores agrícolas AD FS, o Azure AD Connect pede o novo certificado TLS/SSL. Forneça um certificado PFX protegido por palavra-passe para continuar a instalação.

![Certificado TLS/SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Depois de fornecer o certificado, a Azure AD Connect passa por uma série de pré-requisitos. Verifique o certificado para garantir que o certificado está correto para a exploração AD FS:

-   O nome do sujeito/nome do sujeito alternativo para o certificado é o mesmo que o nome de serviço da federação, ou é um certificado wildcard.
-   O certificado é válido por mais de 30 dias.
-   A cadeia de confiança do certificado é válida.
-   O certificado está protegido por senha.

## <a name="step-3-select-servers-for-the-update"></a>Passo 3: Selecione servidores para a atualização

No passo seguinte, selecione os servidores que precisam de ter o certificado TLS/SSL atualizado. Os servidores que estão offline não podem ser selecionados para a atualização.

![Selecione servidores para atualizar](./media/how-to-connect-fed-ssl-update/selectservers.png)

Depois de concluir a configuração, o Azure AD Connect apresenta a mensagem que indica o estado da atualização e fornece uma opção para verificar o súm in AD FS.

![Configuração completa](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>FAQs

* **Qual deve ser o nome do certificado para o novo certificado AD FS TLS/SSL?**

    Azure AD Connect verifica se o nome do sujeito/nome de assunto alternativo do certificado contém o nome de serviço da federação. Por exemplo, se o nome de serviço da sua federação for fs.contoso.com, o nome do sujeito/nome de assunto alternativo deve ser fs.contoso.com.  Os certificados wildcard também são aceites.

* **Por que me pedem credenciais de novo na página do servidor WAP?**

    Se as credenciais que fornece para a ligação aos servidores AD FS também não têm o privilégio de gerir os servidores WAP, então o Azure AD Connect pede credenciais que tenham privilégio administrativo nos servidores WAP.

* **O servidor é mostrado como offline. O que devo fazer?**

    O Azure AD Connect não pode efetuar qualquer operação se o servidor estiver offline. Se o servidor fizer parte da fazenda AD FS, verifique a conectividade com o servidor. Depois de resolver o problema, prima o ícone de atualização para atualizar o estado do assistente. Se o servidor fez parte da quinta mais cedo, mas agora já não existe, clique em **Remover** para eliminá-lo da lista de servidores que o Azure AD Connect mantém. Remover o servidor da lista no Azure AD Connect não altera a própria configuração AD FS. Se estiver a utilizar O FS AD no Windows Server 2016 ou posteriormente, o servidor permanece nas definições de configuração e será mostrado novamente na próxima vez que a tarefa for executada.

* **Posso atualizar um subconjunto dos meus servidores agrícolas com o novo certificado TLS/SSL?**

    Sim. Pode sempre executar novamente o **Certificado SSL de atualização** de tarefas para atualizar os restantes servidores. Na página de atualização de **certificados Select para SSL,** pode classificar a lista de servidores na **data de expiração do SSL** para aceder facilmente aos servidores que ainda não foram atualizados.

* **Removi o servidor na execução anterior, mas ainda está a ser mostrado como offline e listado na página dos Servidores FS da AD. Porque é que o servidor offline ainda lá está mesmo depois de o ter removido?**

    Remover o servidor da lista no Azure AD Connect não o remove na configuração AD FS. Azure AD Connect referências AD FS (Windows Server 2016 ou superior) para qualquer informação sobre a quinta. Se o servidor ainda estiver presente na configuração AD FS, será listado novamente na lista.  

## <a name="next-steps"></a>Passos seguintes

- [Azure AD Connect e federação](how-to-connect-fed-whatis.md)
- [Gestão e personalização de Serviços da Federação de Diretórios Ativos com Azure AD Connect](how-to-connect-fed-management.md)