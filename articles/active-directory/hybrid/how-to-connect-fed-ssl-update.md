---
title: O Azure AD Connect - atualizar o certificado SSL para um farm do AD FS | Documentos da Microsoft
description: Este documento detalha os passos para atualizar o certificado SSL de um farm do AD FS com o Azure AD Connect.
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
ms.openlocfilehash: 39ac0e9cf11a0c6c212c4beadb6635ad2b6b056d
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734715"
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Atualizar o certificado SSL para um farm de serviços de Federação do Active Directory (AD FS)

## <a name="overview"></a>Descrição geral
Este artigo descreve como pode utilizar o Azure AD Connect para atualizar o certificado SSL para um farm de serviços de Federação do Active Directory (AD FS). Pode usar a ferramenta Azure AD Connect para atualizar facilmente o certificado SSL para o farm do AD FS, mesmo que o início de sessão no método do utilizador selecionado não é do AD FS.

Pode executar toda a operação de atualização do certificado SSL para o farm do AD FS em todos os Federação e os servidores de Proxy de aplicações Web (WAP) em três passos simples:

![Três etapas](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Para saber mais sobre os certificados que são utilizados pelo AD FS, veja [certificados de entendimento utilizados pelo AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Pré-requisitos

* **Farm do AD FS**: Certifique-se de que o farm do AD FS é baseado no Windows Server 2012 R2 ou posterior.
* **Do Azure AD Connect**: Certifique-se de que a versão do Azure AD Connect é 1.1.553.0 ou superior. Usará a tarefa **certificado de FS SSL atualização AD**.

![Tarefa de atualização de SSL](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Passo 1: Fornecer informações do farm do AD FS

O Azure AD Connect tenta obter informações sobre o farm do AD FS automaticamente pelo:
1. Consultar as informações de farm do AD FS (Windows Server 2016 ou posterior).
2. Referenciar as informações de execuções anteriores, que são armazenadas localmente com o Azure AD Connect.

É possível modificar a lista de servidores que são apresentados, adicione ou remova os servidores para refletir a configuração atual do farm do AD FS. Assim que as informações do servidor for fornecidas, o Azure AD Connect apresenta a conectividade e o estado do certificado SSL atual.

![Informações do servidor do AD FS](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Se a lista contém um servidor que já não faz parte do farm do AD FS, clique em **remover** para eliminar o servidor da lista de servidores no farm do AD FS.

![Servidor offline na lista](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Remover um servidor da lista de servidores para um farm do AD FS no Azure AD Connect é uma operação local e atualiza as informações para o farm do AD FS que o Azure AD Connect mantém localmente. O Azure AD Connect não altera a configuração do AD FS de modo a refletir a alteração.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Passo 2: Fornecer um novo certificado SSL

Depois de confirmar as informações sobre servidores de farm do AD FS, Azure AD Connect pede para o novo certificado SSL. Fornece um certificado PFX protegido por palavra-passe para continuar a instalação.

![Certificado SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Depois de fornecer o certificado, o Azure AD Connect passa por uma série de pré-requisitos. Verifique se o certificado para se certificar de que o certificado é correto para o farm do AD FS:

-   O nome do requerente alternativo/nome de assunto para o certificado é qualquer um do mesmo que o nome do serviço de Federação ou é um certificado de caráter universal.
-   O certificado é válido durante mais de 30 dias.
-   A cadeia de confiança de certificado é válida.
-   O certificado está protegido de palavra-passe.

## <a name="step-3-select-servers-for-the-update"></a>Passo 3: Selecione os servidores para a atualização

No próximo passo, selecione os servidores que têm de ter o certificado SSL atualizado. Servidores que estão offline não podem ser selecionados para a atualização.

![Selecione os servidores para atualizar](./media/how-to-connect-fed-ssl-update/selectservers.png)

Depois de concluir a configuração, o Azure AD Connect exibe a mensagem que indica o estado da atualização e fornece uma opção para verificar o AD FS início de sessão.

![Configuração completa](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>FAQs

* **O que deve ser o nome do requerente do certificado para o novo certificado SSL do AD FS?**

    O Azure AD Connect verifica se o nome do requerente alternativo/nome de requerente do certificado contém o nome do serviço de Federação. Por exemplo, se o nome do serviço de Federação for fs.contoso.com, o nome de requerente alternativo/nome do requerente tem de ser fs.contoso.com.  Também são aceites certificados de caráter universal.

* **Por que estou eu pedida credenciais novamente na página do servidor WAP?**

    Se as credenciais que fornece para ligar aos servidores AD FS também não tem o privilégio de gerenciar os servidores WAP, em seguida, o Azure AD Connect pede-lhe as credenciais que têm privilégios administrativos nos servidores do WAP.

* **O servidor é mostrado como offline. O que devo fazer?**

    O Azure AD Connect não pode executar qualquer operação se o servidor estiver offline. Se o servidor fizer parte do farm do AD FS, em seguida, verifique a conectividade ao servidor. Depois de ter resolvido o problema, prima o ícone de atualização para atualizar o estado do assistente. Se o servidor tiver sido parte do farm anteriormente, mas agora deixou de existir, clique em **remover** eliminá-lo da lista de servidores que o Azure AD Connect mantém. Remover o servidor a partir da lista no Azure AD Connect não altera a configuração do AD FS em si. Se estiver a utilizar o AD FS no Windows Server 2016 ou posterior, o permanece de servidor nas definições de configuração e será mostrado novamente na próxima vez que a tarefa é executada.

* **Pode atualizar um subconjunto dos meus servidores do farm com o novo certificado SSL?**

    Sim. Pode sempre executar a tarefa **atualizar o certificado de SSL** novamente para atualizar os restantes servidores. Sobre o **selecione os servidores para SSL a atualização do certificado** página, pode ordenar a lista de servidores no **data de expiração SSL** facilmente aceder aos servidores que não forem atualizados ainda.

* **Removi o servidor em execução anterior, mas é ainda a ser mostrado como offline e listados na página de servidores do AD FS. Por que é o servidor offline continuam lá, mesmo depois de eu removi-lo?**

    Remover o servidor a partir da lista no Azure AD Connect não removê-lo na configuração do AD FS. O Azure AD Connect referencia o AD FS (Windows Server 2016 ou superior) para todas as informações sobre o farm. Se o servidor ainda está presente na configuração do AD FS, serão apresentado na lista.  

## <a name="next-steps"></a>Passos Seguintes

- [Azure AD Connect e a federação](how-to-connect-fed-whatis.md)
- [Gestão de serviços de Federação de diretório Active Directory e a personalização com o Azure AD Connect](how-to-connect-fed-management.md)

