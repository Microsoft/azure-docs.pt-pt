---
title: Como gerir os dispositivos utilizando o portal Azure [ Microsoft Docs
description: Aprenda a utilizar o portal Azure para gerir dispositivos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79262246"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gerir identidades do dispositivo utilizando o portal Azure

Com a gestão da identidade do dispositivo no Azure Ative Directory (Azure AD), pode garantir que os seus utilizadores estão a aceder aos seus recursos a partir de dispositivos que cumprem os seus padrões de segurança e conformidade.

Este artigo:

- Assume que está familiarizado com a introdução à gestão de identidade do [dispositivo no Diretório Ativo azure](overview.md)
- Fornece-lhe informações sobre a gestão das identidades do seu dispositivo utilizando o portal Azure AD

## <a name="manage-device-identities"></a>Gerir identidades do dispositivo

O portal Azure AD fornece-lhe um local central para gerir as identidades do seu dispositivo. Pode chegar a este local usando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue em > **Dispositivos** **de Diretório Ativo Azure**.

A página **Dispositivos** permite::

- Configure as definições do seu dispositivo
- Localizar dispositivos
- Executar tarefas de gestão de identidade do dispositivo
- Rever registos de auditoria relacionados com dispositivos  
  
## <a name="configure-device-settings"></a>Configurar definições de dispositivos

Para gerir as identidades do seu dispositivo utilizando o portal Azure AD, os seus dispositivos precisam de ser [registados ou unidos à](overview.md) AD Azure. Como administrador, pode afinar o processo de registo e junção de dispositivos configurando as definições do dispositivo.

A página de definições do dispositivo permite configurar definições relacionadas com as identidades do dispositivo:

![Gerir um dispositivo Intune](./media/device-management-azure-portal/21.png)

- **Os utilizadores podem juntar dispositivos ao Azure AD** - Esta definição permite selecionar os utilizadores que podem registar os seus dispositivos à medida que o Azure AD se juntou a dispositivos. O padrão é **All**.

> [!NOTE]
> **Os utilizadores podem aderir a dispositivos à** definição de AD Azure só se aplica à adesão do Azure AD no Windows 10.

- **Administradores locais adicionais em dispositivos aderes** a AD Azure - Pode selecionar os utilizadores que recebem direitos de administrador local num dispositivo. Os utilizadores adicionados aqui são adicionados ao papel de Administradores de *Dispositivos* em Azure AD. Os administradores globais da AD Azure e os proprietários de dispositivos recebem direitos de administrador local por defeito. Esta opção é uma capacidade de edição premium disponível através de produtos como o Azure AD Premium ou a Enterprise Mobility Suite (EMS).
- **Os utilizadores podem registar os seus dispositivos com a AD Azure** - É necessário configurar esta definição para permitir que os dispositivos pessoais, iOS, Android e macOs do Windows 10 sejam registados com a AD Azure. Se selecionar **Nenhum**, os dispositivos não estão autorizados a registar-se com a AD Azure. A inscrição com a Microsoft Intune ou Mobile Device Management (MDM) para o Office 365 requer registo. Se configurar qualquer um destes serviços, **TODOS** estão selecionados e **NENHUM** está disponível.
- Exija que o **Multi-Factor Auth se junte aos dispositivos** - Pode escolher se os utilizadores são obrigados a fornecer um fator de autenticação adicional para aderir ao seu dispositivo à AD Azure. O padrão é **Nº .** Recomendamos que exija a autenticação de vários fatores ao registar um dispositivo. Antes de ativar a autenticação de vários fatores para este serviço, deve certificar-se de que a autenticação de vários fatores está configurada para os utilizadores que registam os seus dispositivos. Para obter mais informações sobre diferentes serviços de autenticação multifactor Azure, consulte começar com a [autenticação multifactor Azure.](../authentication/concept-mfa-whichversion.md) 

> [!NOTE]
> Requerer que a **Multi-Factor Auth se junte à** definição de dispositivos aplica-se a dispositivos que estejam a aderir a Azure AD ou a Azure AD registada. Esta definição não se aplica aos dispositivos híbridos Azure AD.

- **Número máximo de dispositivos** - Esta definição permite selecionar o número máximo de dispositivos azure AD ou Azure AD registados que um utilizador pode ter em Azure AD. Se um utilizador atingir esta quota, não poderá adicionar dispositivos adicionais até que um ou mais dispositivos existentes sejam removidos. O valor predefinido é **de 20**.

> [!NOTE]
> **O número máximo de dispositivos** de regulação aplica-se a dispositivos que estejam a aderir a Azure AD ou a Azure AD registada. Esta definição não se aplica aos dispositivos híbridos Azure AD.

- **Os utilizadores podem sincronizar definições e dados de aplicações em todos os dispositivos** - Por padrão, esta definição está definida para **NENHUMA**. A seleção de utilizadores ou grupos específicos ou ALL permite que as definições do utilizador e os dados da aplicação se sincronizem nos seus dispositivos Windows 10. Saiba mais sobre como funciona a sincronização no Windows 10.
Esta opção é uma capacidade premium disponível através de produtos como o Azure AD Premium ou a Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Localizar dispositivos

Tem duas opções para localizar dispositivos registados e unidos:

- **Todos os dispositivos** na secção **Gerir** da página **Dispositivos**  
- **Dispositivos** na secção **Gerir** de uma página **de utilizador**

Com ambas as opções, você pode chegar a uma visão que:

- Permite-lhe procurar dispositivos utilizando o nome do ecrã ou identificação do dispositivo como filtro.
- Fornece-lhe uma visão detalhada dos dispositivos registados e unidos
- Permite-lhe executar tarefas comuns de gestão de dispositivos

![Todos os dispositivos](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Se vir um dispositivo que seja "Hybrid Azure AD" com um estado "Pendente" sob a coluna REGISTERED, indica que o dispositivo foi sincronizado a partir da ligação Azure AD e está à espera de concluir o registo do cliente. Leia mais sobre como [planear a sua adi azure híbrida aderir à implementação](hybrid-azuread-join-plan.md). Informações adicionais podem ser encontradas no artigo, [Dispositivos frequentemente questionados.](faq.md)
>
>   ![Dispositivos pendentes](./media/device-management-azure-portal/75.png)
>
>* Para alguns dispositivos iOS, os nomes do dispositivo que contêm apóstrofos podem potencialmente usar diferentes caracteres que se parecem com apóstrofos. Assim, procurar tais dispositivos é um pouco complicado - se não estiver a ver corretamente os resultados da pesquisa, certifique-se de que a cadeia de pesquisa contém caracteres apóstrofos correspondentes.

## <a name="device-identity-management-tasks"></a>Tarefas de gestão de identidade do dispositivo

Como administrador global ou administrador de dispositivos na nuvem, pode gerir os dispositivos registados ou unidos. Os administradores de serviços intune podem:

- Dispositivos de atualização - Exemplos são operações diárias como dispositivos de ativação/desativação
- Eliminar dispositivos – Quando um dispositivo é retirado e deve ser eliminado em Azure AD

Esta secção fornece-lhe informações sobre tarefas comuns de gestão de identidade do dispositivo.

### <a name="manage-an-intune-device"></a>Gerir um dispositivo Intune

Se for um administrador intune, pode gerir dispositivos marcados como **Microsoft Intune**. Se o dispositivo não estiver matriculado com a Microsoft Intune, a opção "Gerir" será acinzentada.

![Gerir um dispositivo Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Ativar / desativar um dispositivo Azure AD

Para ativar/desativar um dispositivo, tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

   ![Gerir um dispositivo Intune](./media/device-management-azure-portal/71.png)

- A barra de ferramentas na página **dispositivos**

   ![Gerir um dispositivo Intune](./media/device-management-azure-portal/32.png)

**Observações:**

- É necessário ser administrador global ou administrador de dispositivos na nuvem em Azure AD para ativar/desativar um dispositivo. 
- A desativação de um dispositivo impede que um dispositivo autentique com sucesso com a AD Azure, evitando assim que o dispositivo aceda aos seus recursos AD Azure que são guardados pelo dispositivo CA ou utilizando as suas credenciais WH4B.
- A desativação do dispositivo revogará tanto o Token de Atualização Primária (PRT) como quaisquer Tokens Refresh (RT) no dispositivo.

### <a name="delete-an-azure-ad-device"></a>Eliminar um dispositivo Azure AD

Para eliminar um dispositivo, tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

   ![Gerir um dispositivo Intune](./media/device-management-azure-portal/72.png)

- A barra de ferramentas na página **dispositivos**

   ![Eliminar um dispositivo](./media/device-management-azure-portal/34.png)

**Observações:**

- É necessário ser administrador global ou administrador intune no Azure AD para apagar um dispositivo.
- Apagar um dispositivo:
   - Impede que um dispositivo aceda aos seus recursos Da D.A. Azure.
   - Remove todos os detalhes que estão ligados ao dispositivo, por exemplo, chaves BitLocker para dispositivos Windows.  
   - Representa uma atividade não recuperável e não é recomendada a menos que seja necessária.

Se um dispositivo for gerido por outra autoridade de gestão (por exemplo, Microsoft Intune), certifique-se de que o dispositivo foi limpo/retirado antes de apagar o dispositivo em Azure AD. Reveja como [gerir dispositivos velhos](device-management-azure-portal.md) antes de apagar quaisquer dispositivos.

### <a name="view-or-copy-device-id"></a>Ver ou copiar id do dispositivo

Pode utilizar um ID do dispositivo para verificar os dados de identificação do dispositivo no dispositivo ou utilizar o PowerShell durante a resolução de problemas. Para aceder à opção de cópia, clique no dispositivo.

![Ver um ID do dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Ver ou copiar teclas BitLocker

Pode visualizar e copiar as teclas BitLocker para ajudar os utilizadores a recuperar em forma a sua unidade encriptada. Estas chaves só estão disponíveis para dispositivos Windows que estejam encriptados e tenham as chaves armazenadas em Azure AD. Pode copiar estas chaves ao aceder aos detalhes do dispositivo.

![Ver teclas BitLocker](./media/device-management-azure-portal/36.png)

Para visualizar ou copiar as teclas BitLocker, tem de ser o proprietário do dispositivo ou um utilizador que tenha pelo menos uma das seguintes funções atribuídas:

- Administrador de dispositivos de nuvem
- Administrador Global
- Administrador de Helpdesk
- Administrador de Serviços do Intune
- Administrador de Segurança
- Leitor de Segurança

> [!NOTE]
> Hybrid Azure AD Os dispositivos Do Windows 10 não têm proprietário. Então, se procura um dispositivo pelo proprietário e não o encontrou, procure pelo ID do dispositivo.

## <a name="audit-logs"></a>Registos de auditoria

As atividades do dispositivo estão disponíveis através dos registos de atividade. Estes registos incluem atividades desencadeadas pelo serviço de registo do dispositivo e pelos utilizadores:

- Criação de dispositivos e adição de proprietários/utilizadores no dispositivo
- Alterações nas definições do dispositivo
- Operações do dispositivo, tais como a apagar ou atualizar um dispositivo

O seu ponto de entrada para os dados de auditoria é **Registos de Auditoria** na secção **Atividade** da página **Dispositivos.**

O registo de auditoria tem uma visão de lista predefinida que mostra:

- A data e a hora da ocorrência
- Os alvos
- O iniciador /ator (que) de uma atividade
- A atividade (o que)

![Registos de auditoria](./media/device-management-azure-portal/63.png)

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Registos de auditoria](./media/device-management-azure-portal/64.png)

Para limitar os dados comunicados a um nível que funcione para si, pode filtrar os dados de auditoria através dos seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (Ator)

Além dos filtros, pode procurar entradas específicas.

![Registos de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Passos seguintes

[Como gerir dispositivos velhos em Azure AD](manage-stale-devices.md)
