---
title: Validação controlada de associação do Azure AD híbrido - Azure AD
description: Aprenda a efetuar uma validação controlada de associação do híbrida do Azure AD antes de a ativar toda a organização em simultâneo
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: article
ms.date: 05/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5b388f92a875fb2635037a6eae3ff3b6a95793
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66513290"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Validação controlada de associação do Azure AD híbrido

Quando todos os pré-requisitos estão em vigor, serão registado automaticamente dispositivos Windows como dispositivos no seu inquilino do Azure AD. O estado destas identidades de dispositivo no Azure AD é referido como a associação do Azure AD híbrido. Mais informações sobre os conceitos abordados neste artigo podem ser encontradas nos artigos [introdução à gestão de dispositivos no Azure Active Directory](overview.md) e [planejar sua implementação híbrida do Azure Active Directory ](hybrid-azuread-join-plan.md).

As organizações, poderão querer fazer uma validação controlada de associação do híbrida do Azure AD antes de a ativar toda a organização inteira ao mesmo tempo. Este artigo explicará como realizar uma validação controlada de associação do Azure AD híbrido.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Validação controlada de associação do Azure AD híbrido em dispositivos atuais do Windows

Para dispositivos com o sistema de operativo ambiente de trabalho do Windows, a versão suportada é a atualização de aniversário do Windows 10 (versão 1607) ou posterior. Como melhor prática, atualize para a versão mais recente do Windows 10.

Para fazer uma validação controlada de associação do Azure AD híbrido em dispositivos atuais do Windows, terá de:

1. Limpar a entrada de ponto de ligação de serviço (SCP) do Active Directory (AD), se existir
1. Configurar a definição de registo do lado do cliente para SCP nos seus computadores associados a um domínio com um objeto de política de grupo (GPO)
1. Se estiver a utilizar o AD FS, tem também de configurar a definição de registo do lado do cliente para o SCP no seu servidor do AD FS com um GPO  



### <a name="clear-the-scp-from-ad"></a>Limpar o SCP do AD

Utilize o Editor de Interfaces do serviços do Active Directory (ADSI Edit) para modificar os objetos de SCP no AD.

1. Iniciar o **ADSI Edit** aplicativo de área de trabalho de e estação de trabalho administrativa ou um controlador de domínio como um administrador empresarial.
1. Ligar para o **contexto de nomenclatura de configuração** do seu domínio.
1. Navegue até **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = Configuration de registo do dispositivo**
1. Clique com o botão direito do rato no objeto folha sob **CN = Configuration de registo do dispositivo** e selecione **propriedades**
   1. Selecione **palavras-chave** partir do **Editor de atributos** janela e clique em **editar**
   1. Selecionar os valores da **azureADId** e **azureADName** (um de cada vez) e clique em **remover**
1. Fechar **Editor de ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurar a definição de registo do lado do cliente para SCP

Utilize o exemplo a seguir para criar um objeto de política de grupo (GPO) para implementar uma definição de registo configurar uma entrada de SCP no registo dos seus dispositivos.

1. Abra uma consola de gestão de política de grupo e crie um novo objeto de política de grupo no seu domínio.
   1. Forneça um nome (por exemplo, ClientSideSCP) de seu GPO recém-criado.
1. Editar o GPO e localize o seguinte caminho: **Configuração do computador** > **preferências** > **definições do Windows** > **registo**
1. Com o botão direito no Registro e selecione **New** > **Item do registo**
   1. Sobre o **gerais** separador, configure o seguinte
      1. Ação: **Atualização**
      1. Ramo de registo: **HKEY_LOCAL_MACHINE**
      1. Caminho da chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome do valor: **TenantId**
      1. Tipo de valor: **REG_SZ**
      1. Dados do valor: O GUID ou **ID de diretório** da sua instância do Azure AD (este valor pode ser encontrado na **portal do Azure** > **do Azure Active Directory**  >   **Propriedades** > **ID do diretório**)
   1. Clique em **OK**
1. Com o botão direito no Registro e selecione **New** > **Item do registo**
   1. Sobre o **gerais** separador, configure o seguinte
      1. Ação: **Atualização**
      1. Ramo de registo: **HKEY_LOCAL_MACHINE**
      1. Caminho da chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome do valor: **TenantName**
      1. Tipo de valor: **REG_SZ**
      1. Dados do valor: Sua verificado **nome de domínio** no Azure AD (por exemplo, `contoso.onmicrosoft.com` ou qualquer outro nome de domínio verificado no seu diretório)
   1. Clique em **OK**
1. Feche o editor para o GPO recém-criado
1. Ligação GPO recém-criado à UO desejada que contém computadores associados a domínios que pertencem a sua população de implementação controladas

### <a name="configure-ad-fs-settings"></a>Configurar definições do AD FS

Se estiver a utilizar o AD FS, tem primeiro de configurar o SCP do lado do cliente com as instruções mencionadas acima, mas o GPO de ligação para os seus servidores do AD FS. Esta configuração é necessária para o AD FS estabelecer a origem de identidades de dispositivos como o Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Validação controlada de associação do Azure AD híbrido em dispositivos de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, tem de instalar as organizações [Microsoft Workplace Join para computadores Windows de 10](https://www.microsoft.com/download/details.aspx?id=53554) disponível no Microsoft Download Center.

Pode implementar o pacote com um sistema de distribuição de software, como [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote suporta as opções de instalação silenciosa padrão com o parâmetro silencioso. O current branch do Configuration Manager oferece vantagens significativas em relação às versões anteriores, como a capacidade de controlar registos concluídos.

O instalador cria uma tarefa agendada no sistema que é executado no contexto de utilizador. A tarefa é acionada quando o utilizador inicia sessão Windows. A tarefa silenciosamente associa o dispositivo com o Azure AD com as credenciais de utilizador depois de autenticar com o Azure AD.

Para controlar o registo de dispositivos, deve implementar o pacote do Windows Installer para seu grupo selecionado de dispositivos de nível inferior do Windows.

> [!NOTE]
> Se um SCP não está configurado no AD, em seguida, deve seguir a mesma abordagem, conforme descrito para [configurar a definição de registo do lado do cliente para SCP](#configure-client-side-registry-setting-for-scp)) nos seus computadores associados a um domínio com um objeto de política de grupo (GPO).


Depois de verificar que tudo funciona conforme esperado, pode registar automaticamente o resto dos seus dispositivos de atuais e de nível inferior do Windows com o Azure AD por [configuração do SCP com o Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Passos Seguintes

[Planear a sua implementação híbrida do Azure Active Directory](hybrid-azuread-join-plan.md)
