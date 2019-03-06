---
title: Controlar a associação do Azure AD híbrido dos seus dispositivos | Documentos da Microsoft
description: Saiba como controlar a associação do Azure AD híbrido dos seus dispositivos no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b113dd3e354e778d2cf16182665afff5440d2e5
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408535"
---
# <a name="control-the-hybrid-azure-ad-join-of-your-devices"></a>Controlar a associação híbrida do Azure AD dos seus dispositivos

Associação ao Azure Active Directory (Azure AD) de híbrido é um processo para registrar automaticamente os seus dispositivos de associados a um domínio no local com o Azure AD. Há casos em que não pretende todos os seus dispositivos sejam registrados automaticamente. Isso é verdade, por exemplo, durante a implementação inicial para verificar que tudo funciona conforme esperado.

Este artigo fornece orientações sobre como pode controlar a associação do Azure AD híbrido dos seus dispositivos. 


## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que esteja familiarizado com:

-  [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introdução à gestão de dispositivos no Azure Active Directory)
 
-  [Planear a sua implementação híbrida do Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Associação ao Azure Active Directory configurar híbrido para domínios geridos](hybrid-azuread-join-managed-domains.md) ou [associação ao Azure Active Directory configurar híbrido para domínios federados](hybrid-azuread-join-federated-domains.md)



## <a name="control-windows-current-devices"></a>Controlar os dispositivos atuais do Windows

Para dispositivos com o sistema de operativo ambiente de trabalho do Windows, a versão suportada é a atualização de aniversário do Windows 10 (versão 1607) ou posterior. Como melhor prática, atualize para a versão mais recente do Windows 10.

Início de sessão no Windows de todos os dispositivos atuais registem automaticamente com o Azure AD no início do dispositivo ou utilizador. Pode controlar esse comportamento ao utilizar um objeto de política de grupo (GPO) ou o System Center Configuration Manager.

Para controlar os dispositivos atuais do Windows, terá de: 


1.  **Para todos os dispositivos**: Desative o registo automático de dispositivos.
2.  **Para dispositivos selecionados**: Ative o registo automático de dispositivos.

Depois de verificar que tudo funciona conforme esperado, está pronto para ativar o registo automático de dispositivos para todos os dispositivos novamente.



### <a name="group-policy-object"></a>Objeto de diretiva de grupo 

Pode controlar o comportamento de registro de dispositivo dos seus dispositivos ao implementar o GPO seguinte: **Registe-se os computadores associados a um domínio, como dispositivos**.

Para definir o GPO:

1.  Open **Gestor de servidores**e, em seguida, aceda à **ferramentas** > **gerenciamento de diretiva de grupo**.

2.  Vá para o nó de domínio que corresponde ao domínio em que pretende desativar ou ativar o registo automático.

3.  Com o botão direito **objetos de política de grupo**e, em seguida, selecione **New**.

4.  Introduza um nome (por exemplo, **associação ao Azure AD híbrido**) para o objeto de política de grupo. 

5.  Selecione **OK**.

6.  Clique no seu novo GPO e, em seguida, selecione **editar**.

7.  Aceda a **configuração do computador** > **políticas** > **modelos administrativos** > **Windows Componentes** > **registo de dispositivos**. 

8.  Com o botão direito **registar computadores associados a domínios como dispositivos**e, em seguida, selecione **editar**.

    > [!NOTE] 
    > Este modelo de política de grupo foi alterado de versões anteriores do console de gerenciamento de diretiva de grupo. Se estiver a utilizar uma versão anterior da consola, aceda a **configuração do computador** > **políticas** > **modelos administrativos**  >  **Componentes do Windows** > **registo de dispositivos** > **associados a um domínio de Registre-se o computador como dispositivo**. 

9.  Selecione uma das seguintes definições e, em seguida, selecione **aplicar**:

    - **Desativado**: Para impedir que o registo automático de dispositivos.
    - **Ativado**: Para ativar o registo automático de dispositivos.

10. Selecione **OK**.

Precisa vincular o GPO a um local de sua preferência. Por exemplo, para definir esta política para todos os dispositivos em atuais associado a um domínio na sua organização, ligue o GPO ao domínio. Para fazer uma implantação controlada, defina esta política para dispositivos associados a domínios Windows atuais que pertencem a uma unidade organizacional ou um grupo de segurança.

### <a name="configuration-manager-controlled-deployment"></a>Implantação do Configuration Manager controlado 

Pode controlar o comportamento de registro de dispositivo dos seus dispositivos atuais ao configurar a definição de cliente seguinte: **Registar automaticamente novos dispositivos associados a um domínio do Windows 10 com o Azure Active Directory**.

Para configurar a definição de cliente:

1.  Open **Configuration Manager**, selecione **administração**e, em seguida, aceda a **definições de cliente**.

2.  Abra as propriedades **predefinições de cliente** e selecione **serviços Cloud**.

3.  Sob **definições do dispositivo**, selecione uma das seguintes definições para **automaticamente registar novos dispositivos associados a um domínio do Windows 10 com o Azure Active Directory**:

    - **Não**: Para impedir que o registo automático de dispositivos.
    - **Sim**: Para ativar o registo automático de dispositivos.

4.  Selecione **OK**.

Precisa de associar esta definição de cliente para um local de sua preferência. Por exemplo, para configurar esta definição de cliente para todos os dispositivos atuais de Windows na sua organização, ligar o definição de cliente para o domínio. Para fazer uma implantação controlada, pode configurar o definição de cliente para Windows associados a um domínio atuais dispositivos que pertencem a uma unidade organizacional ou um grupo de segurança.

> [!Important]
> Embora a configuração anterior se encarrega de dispositivos Windows 10 associados a domínios existentes, os dispositivos recentemente são ingressar no domínio ainda podem tentar concluir a associação do Azure AD híbrido devido o potencial atraso na aplicação da política de grupo ou Definições do Gestor de configuração nos dispositivos. 
>
> Para evitar esta situação, recomendamos que crie uma nova imagem do Sysprep (utilizada como um exemplo para um método de aprovisionamento). Criá-la a partir de um dispositivo que foi anteriormente nunca híbridos associados ao Azure AD e o que já tem a política de grupo a configuração ou definição do cliente do Configuration Manager aplicada. Também tem de utilizar a nova imagem para o aprovisionamento de novos computadores associar o domínio da sua organização. 

## <a name="control-windows-down-level-devices"></a>Controlar dispositivos de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, terá de transferir e instalar o pacote do Windows Installer (. msi) do Centro de transferências no [Microsoft Workplace Join para computadores Windows de 10](https://www.microsoft.com/download/details.aspx?id=53554) página.

Pode implementar o pacote com um sistema de distribuição de software, como [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote suporta as opções de instalação silenciosa padrão com o parâmetro silencioso. O current branch do Configuration Manager oferece vantagens significativas em relação às versões anteriores, como a capacidade de controlar registos concluídos.

O instalador cria uma tarefa agendada no sistema que é executado no contexto do usuário. A tarefa é acionada quando o utilizador inicia sessão Windows. A tarefa silenciosamente associa o dispositivo com o Azure AD com as credenciais de utilizador depois de autenticar com o Azure AD.

Para controlar o registo de dispositivos, deve implementar o pacote do Windows Installer apenas a um grupo selecionado de dispositivos de nível inferior do Windows. Se tiver verificado que tudo funciona conforme esperado, está pronto para distribuir o pacote para todos os dispositivos de nível inferior.


## <a name="next-steps"></a>Passos Seguintes

* [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introdução à gestão de dispositivos no Azure Active Directory)



