---
title: Como gerir dispositivos utilizando o portal Azure | Microsoft Docs
description: Saiba como utilizar o portal Azure para gerir dispositivos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 09/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4269ba32a22538eeba35ba484fae0c98cd219b7c
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952784"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gerir identidades de dispositivos com o portal do Azure

A Azure AD fornece-lhe um lugar central para gerir identidades do dispositivo.

A página **Todos os dispositivos** permite:00:

- Identificar dispositivos, incluindo:
   - Dispositivos que tenham sido aderidos ou registados no Azure AD.
   - Dispositivos implantados com o [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot).
   - Impressoras usando [impressão universal](/universal-print/fundamentals/universal-print-getting-started)
- Execute tarefas de gestão de identidade do dispositivo como ativar, desativar, eliminar ou gerir.
   - [As impressoras](/universal-print/fundamentals/) e os dispositivos [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) têm opções de gestão limitadas no Azure AD. Devem ser geridos a partir das respetivas interfaces de administração.
- Configure as definições de identidade do seu dispositivo.
- Ativar ou desativar o Roaming do Estado da Empresa.
- Rever registos de auditoria relacionados com dispositivos

[![Todos os dispositivos visualizam no portal Azure](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

Pode aceder ao portal dos dispositivos utilizando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue por dispositivos **de diretório ativo Azure**  >  .

## <a name="manage-devices"></a>Gerir dispositivos

Existem duas localizações para gerir dispositivos em Azure AD:

- **Portal Azure**  >  Diretório Ativo **Azure**  >  **Dispositivos**
- **Portal Azure**  >  Diretório Ativo **Azure**  >  **Utilizadores** > Selecionar um utilizador > **dispositivos**

Ambas as opções permitem aos administradores a capacidade de:

- Procure por dispositivos.
- Consulte os detalhes do dispositivo, incluindo:
    - Nome do dispositivo
    - ID do Dispositivo
    - OS e Versão
    - Tipo de associação
    - Proprietário
    - Gestão e conformidade de dispositivos móveis
    - Chave de recuperação BitLocker
- Executar tarefas de gestão de identidade do dispositivo como, ativar, desativar, eliminar ou gerir.
   - [As impressoras](/universal-print/fundamentals/) e os dispositivos [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) têm opções de gestão limitadas no Azure AD. Devem ser geridos a partir das respetivas interfaces de administração.

> [!TIP]
> - O Hybrid AZure AD Juntou-se aos dispositivos windows 10 que não possuem um proprietário. Se estiver à procura de um dispositivo pelo proprietário e não o encontrar, procure pelo ID do dispositivo.
>
> - Se vir um dispositivo que seja "Hybrid Azure AD" com um estado "Pendente" sob a coluna REGISTERED, indica que o dispositivo foi sincronizado a partir da ligação AD AD do Azure e aguarda a inscrição completa do cliente. Leia mais sobre como planear a [sua Ad Hybrid Azure aderir à implementação.](hybrid-azuread-join-plan.md) Informações adicionais podem ser encontradas no artigo, [Dispositivos frequentemente questionados](faq.md).
>
> - Para alguns dispositivos iOS, os nomes do dispositivo que contêm apóstrofos podem potencialmente usar diferentes caracteres que se parecem com apóstrofos. Assim, procurar por estes dispositivos é um pouco complicado - se não estiver a ver os resultados da pesquisa corretamente, certifique-se de que a cadeia de pesquisa contém caracteres apóstrofo correspondentes.

### <a name="manage-an-intune-device"></a>Gerir um dispositivo Intune

Se for administrador da Intune, pode gerir dispositivos onde o MDM está marcado com a **Marca Microsoft Intune.** Se o dispositivo não estiver matriculado com o Microsoft Intune, a opção "Gerir" será acinzentado.

### <a name="enable-or-disable-an-azure-ad-device"></a>Ativar ou desativar um dispositivo AD Azure

Para ativar ou desativar dispositivos, tem duas opções:

- A barra de ferramentas na página **todos os dispositivos** após a seleção de um ou mais dispositivos.
- A barra de ferramentas depois de perfurar para baixo num dispositivo específico.

> [!IMPORTANT]
> - Tem de ser administrador global ou administrador de dispositivos em nuvem no Azure AD para ativar ou desativar um dispositivo. 
> - A desativação de um dispositivo impede que um dispositivo autente com sucesso com a Azure AD, impedindo assim que o dispositivo aceda aos seus recursos Azure AD protegidos pelo Acesso Condicional baseado no dispositivo ou utilizando credenciais do Windows Hello para negócios.
> - A desativação de um dispositivo revogará tanto o Token de Atualização Primária (PRT) como quaisquer Tokens refresh (RT) no dispositivo.
> - As impressoras não podem ser ativadas ou desativadas em Azure AD.

### <a name="delete-an-azure-ad-device"></a>Eliminar um dispositivo AD Azure

Para eliminar um dispositivo, tem duas opções:

- A barra de ferramentas na página **todos os dispositivos** após a seleção de um ou mais dispositivos.
- A barra de ferramentas depois de perfurar para baixo num dispositivo específico.

> [!IMPORTANT]
> - Deve ser-lhe atribuído o administrador do dispositivo em nuvem, administrador Intune ou o papel de administrador global no AD Azure para eliminar um dispositivo.
> - Impressoras e dispositivos Piloto Automático Windows não podem ser eliminados em Azure AD
> - Apagar um dispositivo:
>    - Impede que um dispositivo aceda aos seus recursos Azure AD.
>    - Remove todos os detalhes que estão ligados ao dispositivo, por exemplo, as teclas BitLocker para dispositivos Windows.  
>    - Representa uma atividade não recuperável e não é recomendada a menos que seja necessária.

Se um dispositivo for gerido por outra autoridade de gestão (por exemplo, Microsoft Intune), certifique-se de que o dispositivo foi limpo/retirado antes de eliminar o dispositivo em Azure AD. Reveja como [gerir dispositivos antigos](manage-stale-devices.md) antes de eliminar quaisquer dispositivos.

### <a name="view-or-copy-device-id"></a>Ver ou copiar iD do dispositivo

Pode utilizar um ID do dispositivo para verificar os detalhes do ID do dispositivo no dispositivo ou utilizar o PowerShell durante a resolução de problemas. Para aceder à opção de cópia, clique no dispositivo.

![Ver um ID do dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Ver ou copiar teclas BitLocker

Pode visualizar e copiar as teclas BitLocker para permitir que os utilizadores recuperem unidades encriptadas. Estas chaves só estão disponíveis para dispositivos Windows encriptados e têm as suas chaves armazenadas no AZure AD. Pode encontrar estas teclas ao aceder aos detalhes de um dispositivo selecionando a **Chave de Recuperação do Espetáculo**. Selecionar a **Chave de Recuperação de Espetáculos** gerará um registo de auditoria, que pode encontrar na `KeyManagement` categoria.

![Ver teclas BitLocker](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Para visualizar ou copiar as teclas BitLocker, tem de ser o proprietário do dispositivo ou um utilizador que tenha pelo menos uma das seguintes funções atribuídas:

- Administrador de dispositivos de nuvem
- Administrador Global
- Administrador helpdesk
- Administrador de Serviços do Intune
- Administrador de Segurança
- Leitor de Segurança

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
1. Navegue por dispositivos **de diretório ativo Azure**  >  .
1. Selecione o banner que diz, **Experimente os novos dispositivos filtrando melhorias. Clique para ativar a pré-visualização.**

Terá agora a capacidade de **adicionar filtros** à sua vista **de todos os dispositivos.**

## <a name="configure-device-settings"></a>Configurar definições de dispositivos

Para gerir as identidades dos dispositivos utilizando o portal AD Azure, estes dispositivos precisam de ser [registados ou unidos](overview.md) ao Azure AD. Como administrador, pode controlar o processo de registo e junção de dispositivos configurando as seguintes definições do dispositivo.

Deve ser-lhe atribuída uma das seguintes funções para visualizar ou gerir as definições do dispositivo no portal Azure:

- Administrador global
- Administrador de dispositivos em nuvem
- Leitor global
- Leitor de diretório

![Definições do dispositivo relacionadas com Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Os utilizadores podem juntar-se a dispositivos para Azure AD** - Esta definição permite selecionar os utilizadores que podem registar os seus dispositivos como dispositivos aderentes Azure AD. O padrão é **All**.

> [!NOTE]
> **Os utilizadores podem aderir a dispositivos à definição de AD do Azure apenas** é aplicável à junção AZure AD no Windows 10. Esta definição não se aplica aos dispositivos híbridos Azure AD, [a Azure AD juntou VMs em Azure](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) e Azure AD dispositivos que utilizam [o modo de auto-implantação do Windows Autopilot,](/mem/autopilot/self-deploying) uma vez que estes métodos funcionam num contexto sem utilizador.

- **Administradores locais adicionais em dispositivos aderidos a Azure AD** - Pode selecionar os utilizadores a quem são concedidos direitos de administrador local num dispositivo. Estes utilizadores são adicionados à função *de Administradores de Dispositivos* no Azure AD. Administradores globais em Azure AD e proprietários de dispositivos recebem direitos de administrador local por defeito. Esta opção é uma capacidade de edição premium disponível através de produtos como o Azure AD Premium ou o Enterprise Mobility Suite (EMS).
- **Os utilizadores podem registar os seus dispositivos com Azure AD** - É necessário configurar esta definição para permitir que os dispositivos pessoais, iOS, Android e macOS do Windows 10 sejam registados com Azure AD. Se selecionar **Nenhum,** os dispositivos não podem registar-se no Azure AD. A inscrição com o Microsoft Intune ou Mobile Device Management (MDM) para o Microsoft 365 requer registo. Se tiver configurado algum destes serviços, **ALL** está selecionado e **nenhum** está disponível.
- **Os dispositivos a aderir ao Azure AD ou a Azure AD registados requerem autenticação multi-factor** - Pode escolher se os utilizadores são obrigados a fornecer um fator de autenticação adicional para se juntarem ou registarem o seu dispositivo no Azure AD. O padrão é **nº**. Recomendamos que exija a autenticação de vários fatores ao registar ou juntar um dispositivo. Antes de ativar a autenticação de vários fatores para este serviço, deve certificar-se de que a autenticação de vários fatores está configurada para os utilizadores que registam os seus dispositivos. Para obter mais informações sobre diferentes serviços de autenticação multi-factor Azure AD, consulte [a autenticação multi-factor Azure AD](../authentication/concept-mfa-howitworks.md). 

> [!NOTE]
> **Os dispositivos a aderir ao AZure AD ou a Azure AD registados requerem que a definição de autenticação multi-factor** se aplique a dispositivos que estejam ligados ao Azure AD (com algumas exceções) ou a Azure AD registada. Esta definição não se aplica aos dispositivos híbridos Azure AD, [a Azure AD juntou VMs em Azure](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) e Azure AD dispositivos de ligação com [o Modo de Auto-implantação do Windows Autopilot](/mem/autopilot/self-deploying).

> [!IMPORTANT]
> - Recomendamos a utilização de [uma ação do utilizador "Registar ou juntar dispositivos"](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) no Acesso Condicional para impor a autenticação de vários fatores para a junção ou registo de um dispositivo. 
> - Tem de definir esta definição para **Não** se estiver a utilizar a política de Acesso Condicional para exigir a authencação de vários fatores. 

- **Número máximo de dispositivos** - Esta definição permite selecionar o número máximo de dispositivos registados AZURE AD ou Azure AD que um utilizador pode ter no Azure AD. Se um utilizador atingir esta quota, não poderá adicionar dispositivos adicionais até que um ou mais dos dispositivos existentes sejam removidos. O valor predefinido é **de 50**. Pode aumentar o valor até 100 e se introduzir um valor superior a 100, a AZure AD irá defini-lo para 100. Também pode utilizar o valor ilimitado para impor nenhum limite que não os limites de quota existentes.

> [!NOTE]
> **O número máximo de dispositivos** que se aplicam a dispositivos que estejam aderentes a Azure AD ou a Azure AD registados. Esta definição não se aplica aos dispositivos híbridos Azure AD.

- [Roaming de Estado Empresarial](enterprise-state-roaming-overview.md)

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

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Screenshot de uma tabela na secção de Atividades da página dispositivos que lista a data, o alvo, o ator e a atividade para quatro registos de auditoria." border="false":::

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Screenshot mostrando a barra de ferramentas da página dispositivos. O item Colunas é realçado." border="false":::

Para limitar os dados comunicados a um nível que funcione para si, pode filtrar os dados de auditoria através dos seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (Ator)

Além dos filtros, pode procurar entradas específicas.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Screenshot dos controlos de filtro de dados de auditoria, com categoria, tipo de recurso de atividade, atividade, intervalo de datas, alvo e campos de ator e um campo de pesquisa." border="false":::

## <a name="next-steps"></a>Passos seguintes

[Como gerir dispositivos em Azure AD](manage-stale-devices.md)

[Roaming de Estado Empresarial](enterprise-state-roaming-overview.md)
