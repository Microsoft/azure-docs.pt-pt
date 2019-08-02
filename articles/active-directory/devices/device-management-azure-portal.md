---
title: Como gerenciar dispositivos usando o portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para gerenciar dispositivos.
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
ms.openlocfilehash: 7f64beb80d1a11930fee74e669675b39087cade0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562240"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gerenciar identidades de dispositivo usando o portal do Azure

Com o gerenciamento de identidade do dispositivo no Azure Active Directory (AD do Azure), você pode garantir que os usuários estejam acessando seus recursos de dispositivos que atendam aos seus padrões de segurança e conformidade.

Este artigo:

- Presume que você esteja familiarizado com a [introdução ao gerenciamento de identidade do dispositivo no Azure Active Directory](overview.md)
- Fornece informações sobre como gerenciar suas identidades de dispositivo usando o portal do AD do Azure

## <a name="manage-device-identities"></a>Gerir identidades do dispositivo

O portal do AD do Azure fornece um local central para gerenciar suas identidades de dispositivo. Você pode chegar a esse local usando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou seguindo estas etapas manuais:

1. Entre no [portal do Azure](https://portal.azure.com) como administrador.
2. Na barra de navegação à esquerda, clique em **Active Directory**.

   ![Configurar definições do dispositivo](./media/device-management-azure-portal/01.png)

3. Na seção **gerenciar** , clique em **dispositivos**.

   ![Configurar definições do dispositivo](./media/device-management-azure-portal/74.png)

A página **dispositivos** permite que você:

- Definir as configurações do dispositivo
- Localizar dispositivos
- Executar tarefas de gerenciamento de identidade do dispositivo
- Examinar os logs de auditoria relacionados ao dispositivo  
  
## <a name="configure-device-settings"></a>Configurar definições do dispositivo

Para gerenciar suas identidades de dispositivo usando o portal do AD do Azure, seus dispositivos precisam ser [registrados ou ingressados](overview.md) no Azure AD. Como administrador, você pode ajustar o processo de registro e junção de dispositivos definindo as configurações do dispositivo.

![Configurar definições do dispositivo](./media/device-management-azure-portal/22.png)

A página Configurações do dispositivo permite que você configure:

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/21.png)

- **Os usuários podem ingressar dispositivos no Azure ad** – essa configuração permite que você selecione os usuários que podem registrar seus dispositivos como dispositivos ingressados no Azure AD. O padrão é **todos**.

> [!NOTE]
> **Os usuários podem ingressar dispositivos na configuração do Azure ad** é aplicável somente ao ingresso no Azure AD no Windows 10.

- **Administradores locais adicionais em dispositivos ingressados no Azure ad** – você pode selecionar os usuários que recebem direitos de administrador local em um dispositivo. Os usuários adicionados aqui são adicionados à função *Administradores de dispositivo* no Azure AD. Os administradores globais no Azure AD e proprietários de dispositivos recebem direitos de administrador local por padrão. Essa opção é uma funcionalidade de edição Premium disponível por meio de produtos como Azure AD Premium ou o Enterprise Mobility Suite (EMS).
- **Os usuários podem registrar seus dispositivos com o Azure ad** -você precisa definir essa configuração para permitir que dispositivos Windows 10 Personal, Ios, Android e macOs sejam registrados com o Azure AD. Se você selecionar **nenhum**, os dispositivos não terão permissão para se registrar no Azure AD. O registro com Microsoft Intune ou MDM (gerenciamento de dispositivo móvel) para o Office 365 requer registro. Se você tiver configurado qualquer um desses serviços, **todos** serão selecionados e **nenhum** não estará disponível.
- **Exigir autenticação multifator para ingressar em dispositivos** – você pode escolher se os usuários precisam fornecer um fator de autenticação adicional para ingressar seu dispositivo no Azure AD. O padrão é **não**. É recomendável exigir a autenticação multifator ao registrar um dispositivo. Antes de habilitar a autenticação multifator para esse serviço, você deve garantir que a autenticação multifator esteja configurada para os usuários que registram seus dispositivos. Para obter mais informações sobre diferentes serviços de autenticação multifator do Azure, consulte [introdução à autenticação multifator do Azure](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **A configuração exigir autenticação multifator para unir dispositivos** não se aplica a dispositivos ingressados no Azure ad híbrido.

- **Número máximo de dispositivos** – essa configuração permite que você selecione o número máximo de dispositivos que um usuário pode ter no Azure AD. Se um usuário atingir essa cota, ele não poderá adicionar outros dispositivos até que um ou mais dos dispositivos existentes sejam removidos. A cota do dispositivo é contada para todos os dispositivos que estão ingressados no Azure AD ou o Azure AD registrado hoje. O valor padrão é **20**.

> [!NOTE]
> A configuração **número máximo de dispositivos** não se aplica a dispositivos ingressados no Azure ad híbrido.

- **Os usuários podem sincronizar configurações e dados de aplicativo entre dispositivos** – por padrão, essa configuração é definida como **nenhum**. Selecionar usuários ou grupos específicos ou todos permite que as configurações do usuário e os dados do aplicativo sejam sincronizados em seus dispositivos Windows 10. Saiba mais sobre como a sincronização funciona no Windows 10.
Essa opção é uma funcionalidade premium disponível por meio de produtos como Azure AD Premium ou o Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Localizar dispositivos

Você tem duas opções para localizar dispositivos registrados e ingressados:

- **Todos os dispositivos** na seção **gerenciar** da página **dispositivos**  

   ![Todos os dispositivos](./media/device-management-azure-portal/41.png)

- **Dispositivos** na seção **gerenciar** de uma página de **usuário**

   ![Todos os dispositivos](./media/device-management-azure-portal/43.png)

Com as duas opções, você pode obter uma exibição que:

- Permite pesquisar dispositivos usando o nome de exibição ou a ID do dispositivo como filtro.
- Fornece uma visão geral detalhada dos dispositivos registrados e ingressados
- Permite executar tarefas comuns de gerenciamento de dispositivos

![Todos os dispositivos](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Se você vir um dispositivo que é "ingressado no Azure AD híbrido" com um estado "pendente" na coluna registrado, ele indica que o dispositivo foi sincronizado do Azure AD Connect e está aguardando a conclusão do registro do cliente. Leia mais sobre como [planejar sua implementação de ingresso no Azure ad híbrido](hybrid-azuread-join-plan.md). Informações adicionais podem ser encontradas no artigo, perguntas frequentes sobre [dispositivos](faq.md).
>
>   ![Dispositivos pendentes](./media/device-management-azure-portal/75.png)
>
>* Para alguns dispositivos iOS, os nomes de dispositivos que contêm apóstrofos podem potencialmente usar caracteres diferentes que se parecem com apóstrofos. Portanto, Pesquisar por tais dispositivos é um pouco complicado – se você não estiver vendo os resultados da pesquisa corretamente, verifique se a cadeia de caracteres de pesquisa contém o caractere de apóstrofo correspondente.

## <a name="device-identity-management-tasks"></a>Tarefas de gerenciamento de identidade do dispositivo

Como administrador global ou administrador de dispositivo de nuvem, você pode gerenciar os dispositivos registrados ou ingressados. Os administradores de serviço do Intune podem:

- Dispositivos de atualização-exemplos são operações diárias, como habilitar/desabilitar dispositivos
- Excluir dispositivos – quando um dispositivo é desativado e deve ser excluído no Azure AD

Esta seção fornece informações sobre tarefas comuns de gerenciamento de identidade de dispositivo.

### <a name="manage-an-intune-device"></a>Gerenciar um dispositivo do Intune

Se você for um administrador do Intune, poderá gerenciar dispositivos marcados como **Microsoft Intune**.

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Habilitar/desabilitar um dispositivo do Azure AD

Para habilitar/desabilitar um dispositivo, você tem duas opções:

- O menu tarefas ("...") na página **todos os dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/71.png)

- A barra de ferramentas na página **dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/32.png)

**Comentários**

- Você precisa ser um administrador global ou um administrador de dispositivo de nuvem no Azure AD para habilitar/desabilitar um dispositivo. 
- A desabilitação de um dispositivo impede que um dispositivo seja autenticado com êxito com o Azure AD, impedindo que o dispositivo acesse seus recursos do Azure AD protegidos pela AC do dispositivo ou usando suas credenciais do WH4B.

### <a name="delete-an-azure-ad-device"></a>Excluir um dispositivo do Azure AD

Para excluir um dispositivo, você tem duas opções:

- O menu tarefas ("...") na página **todos os dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/72.png)

- A barra de ferramentas na página **dispositivos**

   ![Excluir um dispositivo](./media/device-management-azure-portal/34.png)

**Comentários**

- Você precisa ser um administrador global ou um administrador do Intune no Azure AD para excluir um dispositivo.
- Excluindo um dispositivo:
   - Impede que um dispositivo acesse seus recursos do Azure AD.
   - Remove todos os detalhes anexados ao dispositivo, por exemplo, chaves do BitLocker para dispositivos Windows.  
   - Representa uma atividade não recuperável e não é recomendável a menos que seja necessário.

Se um dispositivo for gerenciado por outra autoridade de gerenciamento (por exemplo, Microsoft Intune), verifique se o dispositivo foi apagado/desativado antes de excluir o dispositivo no Azure AD. Examine como [gerenciar dispositivos obsoletos](device-management-azure-portal.md) antes de excluir qualquer dispositivo.

### <a name="view-or-copy-device-id"></a>Exibir ou copiar a ID do dispositivo

Você pode usar uma ID do dispositivo para verificar os detalhes da ID do dispositivo no dispositivo ou usando o PowerShell durante a solução de problemas. Para acessar a opção de cópia, clique no dispositivo.

![Exibir uma ID do dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Exibir ou copiar chaves do BitLocker

Você pode exibir e copiar as chaves do BitLocker para ajudar os usuários a recuperarem sua unidade criptografada. Essas chaves só estão disponíveis para dispositivos Windows que são criptografados e têm suas chaves armazenadas no Azure AD. Você pode copiar essas chaves ao acessar detalhes do dispositivo.

![Exibir chaves do BitLocker](./media/device-management-azure-portal/36.png)

Para exibir ou copiar as chaves do BitLocker, você precisa ser o proprietário do dispositivo ou um usuário que tenha pelo menos uma das seguintes funções atribuídas:

- Administrador de Dispositivos de Cloud
- Administrador Global
- Administrador de Suporte Técnico
- Administrador de Serviços do Intune
- Administrador de Segurança
- Leitor de Segurança

> [!NOTE]
> Dispositivos Windows 10 associados ao Azure AD híbrido não têm um proprietário. Portanto, se você estiver procurando um dispositivo pelo proprietário e não o encontrar, pesquise pela ID do dispositivo.

## <a name="audit-logs"></a>Registos de auditoria

As atividades do dispositivo estão disponíveis por meio dos logs de atividade. Esses logs incluem atividades disparadas pelo serviço de registro de dispositivo e pelos usuários:

- Criação de dispositivo e adição de proprietários/usuários no dispositivo
- Alterações nas configurações do dispositivo
- Operações de dispositivo, como excluir ou atualizar um dispositivo

O ponto de entrada para os dados de auditoria é **logs de auditoria** na seção **atividade** da página **dispositivos** .

![Registos de auditoria](./media/device-management-azure-portal/61.png)

Um registo de auditoria tem uma vista de lista predefinida que mostra:

- A data e a hora da ocorrência
- Os destinos
- O iniciador/ator (quem) de uma atividade
- A atividade (o que)

![Registos de auditoria](./media/device-management-azure-portal/63.png)

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Registos de auditoria](./media/device-management-azure-portal/64.png)

Para limitar os dados comunicados a um nível que funcione para si, pode filtrar os dados de auditoria através dos seguintes campos:

- Category
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (Ator)

Além dos filtros, você pode pesquisar por entradas específicas.

![Registos de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Passos seguintes

[Como gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
