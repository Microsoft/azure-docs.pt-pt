---
title: Como gerir dispositivos utilizando o portal Azure Microsoft Docs
description: Saiba como utilizar o portal Azure para gerir dispositivos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb9bc0adeaff8fa6e0f0298782d6f3fca35058cf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87025970"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gerir identidades de dispositivos com o portal do Azure

Com a gestão da identidade do dispositivo no Azure Ative Directory (Azure AD), pode garantir que os seus utilizadores estão a aceder aos seus recursos a partir de dispositivos que cumprem os seus padrões de segurança e conformidade.

Este artigo:

- Assume que está familiarizado com a [introdução à gestão de identidade de dispositivos no Azure Ative Directory](overview.md)
- Fornece-lhe informações sobre a gestão das identidades do seu dispositivo utilizando o portal AD AZure

![Todos os dispositivos visualizam no portal Azure](./media/device-management-azure-portal/all-devices-azure-portal.png)

## <a name="manage-device-identities"></a>Gerir identidades do dispositivo

O portal AZure AD fornece-lhe um local central para gerir as identidades do seu dispositivo. Você pode chegar a este lugar usando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue por dispositivos **de diretório ativo Azure**  >  **Devices**.

A página **dispositivos** permite-lhe:

- Configure as definições do seu dispositivo
- Localizar dispositivos
- Executar tarefas de gestão de identidade de dispositivos
- Rever registos de auditoria relacionados com dispositivos  
  
## <a name="configure-device-settings"></a>Configurar definições de dispositivos

Para gerir as identidades do seu dispositivo utilizando o portal AD Azure, os seus dispositivos precisam de ser [registados ou unidos](overview.md) ao Azure AD. Como administrador, pode afinar o processo de registo e junção de dispositivos configurando as definições do dispositivo.

A página de definições do dispositivo permite configurar as definições relacionadas com as identidades do dispositivo:

![Definições do dispositivo relacionadas com Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Os utilizadores podem juntar-se a dispositivos para Azure AD** - Esta definição permite selecionar os utilizadores que podem registar os seus dispositivos como dispositivos aderentes Azure AD. O padrão é **All**.

> [!TIP]
> **Os utilizadores podem aderir a dispositivos à definição de AD do Azure apenas** é aplicável à junção AZure AD no Windows 10.

- **Administradores locais adicionais em dispositivos aderidos a Azure AD** - Pode selecionar os utilizadores a quem são concedidos direitos de administrador local num dispositivo. Os utilizadores aqui adicionados são adicionados à função *de Administradores de Dispositivos* no Azure AD. Administradores globais em Azure AD e proprietários de dispositivos recebem direitos de administrador local por defeito. Esta opção é uma capacidade de edição premium disponível através de produtos como o Azure AD Premium ou o Enterprise Mobility Suite (EMS).
- **Os utilizadores podem registar os seus dispositivos com Azure AD** - É necessário configurar esta definição para permitir que os dispositivos pessoais, iOS, Android e macOS do Windows 10 sejam registados com Azure AD. Se selecionar **Nenhum,** os dispositivos não podem registar-se no Azure AD. A inscrição com a Microsoft Intune ou Mobile Device Management (MDM) para o Office 365 requer registo. Se tiver configurado algum destes serviços, **ALL** está selecionado e **nenhum** está disponível.
- **Exija que o Multi-Factor Auth se junte a dispositivos** - Pode escolher se os utilizadores são obrigados a fornecer um fator de autenticação adicional para se juntarem ao Azure AD. O padrão é **nº**. Recomendamos que exija a autenticação de vários fatores ao registar um dispositivo. Antes de ativar a autenticação de vários fatores para este serviço, deve certificar-se de que a autenticação de vários fatores está configurada para os utilizadores que registam os seus dispositivos. Para obter mais informações sobre diferentes serviços de autenticação multi-factor Azure, consulte [a autenticação multi-factor Azure.](../authentication/concept-mfa-whichversion.md) 

> [!NOTE]
> **Exija que o Multi-Factor Auth se junte à** definição de dispositivos que estejam aderentes a Azure AD ou a AZure AD registados. Esta definição não se aplica aos dispositivos híbridos Azure AD.

- **Número máximo de dispositivos** - Esta definição permite selecionar o número máximo de dispositivos registados AZURE AD ou Azure AD que um utilizador pode ter no Azure AD. Se um utilizador atingir esta quota, não poderá adicionar dispositivos adicionais até que um ou mais dos dispositivos existentes sejam removidos. O valor predefinido é **de 20**.

> [!NOTE]
> **O número máximo de dispositivos** que se aplicam a dispositivos que estejam aderentes a Azure AD ou a Azure AD registados. Esta definição não se aplica aos dispositivos híbridos Azure AD.

- **Os utilizadores podem sincronizar as definições e os dados da aplicação em todos os dispositivos** - Por padrão, esta definição está definida como **NENHUMA**. A seleção de utilizadores ou grupos específicos ou ALL permite que as definições e dados da aplicação do utilizador se sincronizem através dos seus dispositivos Windows 10. Saiba mais sobre como funciona a sincronização no Windows 10.
Esta opção é uma capacidade premium disponível através de produtos como o Azure AD Premium ou o Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Localizar dispositivos

Tem duas opções para localizar dispositivos registados e aderidos:

- **Todos os dispositivos** na secção **Gerir** a página **dispositivos**  
- **Dispositivos** na secção **Gerir** uma página do **Utilizador**

Com ambas as opções, pode ver que:

- Permite-lhe procurar dispositivos utilizando o nome de exibição ou iD do dispositivo como filtro.
- Fornece-lhe uma visão geral detalhada dos dispositivos registados e associados
- Permite-lhe executar tarefas comuns de gestão de dispositivos

>[!TIP]
>
>* Se vir um dispositivo que seja "Hybrid Azure AD" com um estado "Pendente" sob a coluna REGISTERED, indica que o dispositivo foi sincronizado a partir da ligação AD AD do Azure e aguarda a inscrição completa do cliente. Leia mais sobre como planear a [sua Ad Hybrid Azure aderir à implementação.](hybrid-azuread-join-plan.md) Informações adicionais podem ser encontradas no artigo, [Dispositivos frequentemente questionados](faq.md).
>
>* Para alguns dispositivos iOS, os nomes do dispositivo que contêm apóstrofos podem potencialmente usar diferentes caracteres que se parecem com apóstrofos. Assim, procurar por estes dispositivos é um pouco complicado - se não estiver a ver os resultados da pesquisa corretamente, certifique-se de que a cadeia de pesquisa contém caracteres apóstrofo correspondentes.

## <a name="device-identity-management-tasks"></a>Tarefas de gestão de identidade de dispositivo

Como administrador global ou administrador de dispositivos em nuvem, pode gerir os dispositivos registados ou unidos. Os administradores do Serviço Intune podem:

- Dispositivos de atualização - Exemplos são operações diárias tais como dispositivos de ativação/desativação
- Eliminar dispositivos - Quando um dispositivo é retirado e deve ser eliminado em Azure AD

Esta secção fornece-lhe informações sobre tarefas comuns de gestão de identidade de dispositivo.

### <a name="manage-an-intune-device"></a>Gerir um dispositivo Intune

Se for um administrador intune, pode gerir dispositivos marcados como **Microsoft Intune**. Se o dispositivo não estiver matriculado com o Microsoft Intune, a opção "Gerir" será acinzentado.

![Gerir um dispositivo Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Ativar / desativar um dispositivo AD Azure

Para ativar/desativar um dispositivo, tem duas opções:

- O menu de tarefas ("...") na página **todos os dispositivos**

   ![Gerir um dispositivo Intune](./media/device-management-azure-portal/71.png)

- A barra de ferramentas na página **dispositivos**

   ![Gerir um dispositivo Intune](./media/device-management-azure-portal/32.png)

**Observações:**

- Tem de ser um administrador global ou administrador de dispositivos em nuvem no AZure AD para ativar/desativar um dispositivo. 
- A desativação de um dispositivo impede que um dispositivo autente com sucesso com a Azure AD, impedindo assim que o dispositivo aceda aos seus recursos AZure AD que são guardados pelo dispositivo CA ou utilizando as suas credenciais WH4B.
- A desativação do dispositivo revogará tanto o Token de Atualização Primária (PRT) como quaisquer Tokens refresh (RT) no dispositivo.

### <a name="delete-an-azure-ad-device"></a>Eliminar um dispositivo AD Azure

Para eliminar um dispositivo, tem duas opções:

- O menu de tarefas ("...") na página **todos os dispositivos**

   ![Gerir um dispositivo Intune](./media/device-management-azure-portal/72.png)

- A barra de ferramentas na página **dispositivos**

   ![Eliminar um dispositivo](./media/device-management-azure-portal/34.png)

**Observações:**

- Tem de ser um administrador global ou um administrador intune no Azure AD para eliminar um dispositivo.
- Apagar um dispositivo:
   - Impede que um dispositivo aceda aos seus recursos Azure AD.
   - Remove todos os detalhes que estão ligados ao dispositivo, por exemplo, as teclas BitLocker para dispositivos Windows.  
   - Representa uma atividade não recuperável e não é recomendada a menos que seja necessária.

Se um dispositivo for gerido por outra autoridade de gestão (por exemplo, Microsoft Intune), certifique-se de que o dispositivo foi limpo/retirado antes de eliminar o dispositivo em Azure AD. Reveja como [gerir dispositivos antigos](manage-stale-devices.md) antes de eliminar quaisquer dispositivos.

### <a name="view-or-copy-device-id"></a>Ver ou copiar iD do dispositivo

Pode utilizar um ID do dispositivo para verificar os detalhes do ID do dispositivo no dispositivo ou utilizar o PowerShell durante a resolução de problemas. Para aceder à opção de cópia, clique no dispositivo.

![Ver um ID do dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Ver ou copiar teclas BitLocker

Pode visualizar e copiar as teclas BitLocker para ajudar os utilizadores a recuperar a sua unidade encriptada. Estas chaves só estão disponíveis para dispositivos Windows encriptados e têm as suas chaves armazenadas no AZure AD. Pode copiar estas teclas ao aceder aos detalhes do dispositivo.

![Ver teclas BitLocker](./media/device-management-azure-portal/36.png)

Para visualizar ou copiar as teclas BitLocker, tem de ser o proprietário do dispositivo ou um utilizador que tenha pelo menos uma das seguintes funções atribuídas:

- Administrador de dispositivos de nuvem
- Administrador Global
- Administrador helpdesk
- Administrador de Serviços do Intune
- Administrador de Segurança
- Leitor de Segurança

> [!NOTE]
> O Hybrid AZure AD Juntou-se aos dispositivos windows 10 que não possuem um proprietário. Então, se está à procura de um dispositivo pelo proprietário e não o encontrou, procure pela identificação do dispositivo.

### <a name="device-list-filtering-preview"></a>Filtragem da lista de dispositivos (pré-visualização)

Anteriormente, só era possível filtrar a lista de dispositivos por atividade e estado ativado. Esta pré-visualização permite filtrar a lista de dispositivos pelos seguintes atributos num dispositivo:

- Estado habilitado
- Estado conforme
- Tipo de Junção (Azure AD juntou-se, Hybrid Azure AD juntou-se, Azure AD registrado)
- Carimbo de data/hora da atividade
- SO
- Tipo de dispositivo (Impressoras, VMs seguros, dispositivos partilhados, dispositivos registados)

Para ativar a funcionalidade de filtragem de pré-visualização na vista **de todos os dispositivos:**

![Ativar a funcionalidade de pré-visualização da filtragem](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue por dispositivos **de diretório ativo Azure**  >  **Devices**.
1. Selecione o banner que diz, **Experimente os novos dispositivos filtrando melhorias. Clique para ativar a pré-visualização.**

Terá agora a capacidade de **adicionar filtros** à sua vista **de todos os dispositivos.**

## <a name="audit-logs"></a>Registos de auditoria

As atividades do dispositivo estão disponíveis através dos registos de atividade. Estes registos incluem atividades desencadeadas pelo serviço de registo do dispositivo e pelos utilizadores:

- Criação de dispositivos e adição de proprietários/utilizadores no dispositivo
- Alterações nas definições do dispositivo
- Operações do dispositivo, tais como a eliminação ou a atualização de um dispositivo

O seu ponto de entrada para os dados de auditoria são **registos** de auditoria na secção **Atividade** da página **dispositivos.**

O registo de auditoria tem uma visão de lista padrão que mostra:

- A data e a hora da ocorrência
- Os alvos
- O iniciador/ator (que) de uma atividade
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

[Como gerir dispositivos em Azure AD](manage-stale-devices.md)
