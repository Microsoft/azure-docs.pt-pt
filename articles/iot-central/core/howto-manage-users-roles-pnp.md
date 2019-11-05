---
title: Gerenciar usuários e funções no aplicativo IoT Central do Azure | Microsoft Docs
description: Como administrador, como gerenciar usuários e funções no aplicativo IoT Central do Azure
author: lmasieri
ms.author: lmasieri
ms.date: 10/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c51af15d2334e873c8c7c399322fbd1279de3784
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468188"
---
# <a name="manage-users-and-roles-in-your-iot-central-application-preview-features"></a>Gerenciar usuários e funções em seu aplicativo IoT Central (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este artigo descreve como, como administrador, você pode adicionar, editar e excluir usuários em seu aplicativo de IoT Central do Azure. O artigo também descreve como gerenciar funções no aplicativo IoT Central do Azure.

Para acessar e usar a seção **Administração** , você deve estar na função **administrador** para um aplicativo IOT central do Azure. Se você criar um aplicativo de IoT Central do Azure, você será adicionado automaticamente à função de **administrador** para esse aplicativo.

## <a name="add-users"></a>Adicionar utilizadores

Cada usuário deve ter uma conta de usuário antes que possa entrar e acessar um aplicativo de IoT Central do Azure. As contas da Microsoft (MSAs) e Azure Active Directory (Azure AD) têm suporte no IoT Central do Azure. Atualmente, não há suporte para grupos de Azure Active Directory no IoT Central do Azure.

Para obter mais informações, consulte [conta Microsoft ajuda](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [início rápido: adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para adicionar um usuário a um aplicativo IoT Central, vá para a página **usuários** na seção **Administração** .
    
    > [!div class="mx-imgBorder"]
    >![Gerir utilizadores](media/howto-manage-users-roles-pnp/manage-users-pnp.png)

1. Para adicionar um usuário, na página **usuários** , escolha **+ Adicionar usuário**.

1. Escolha uma função para o usuário no menu suspenso **função** . Saiba mais sobre as funções na seção [gerenciar funções](#manage-roles) deste artigo.

    > [!div class="mx-imgBorder"]
    >![Adicionar usuário e selecionar uma função](media/howto-manage-users-roles-pnp/add-user-pnp.png)

    > [!NOTE]
    > Um usuário que está em uma função personalizada que concede a eles a permissão para adicionar outros usuários só pode adicionar usuários a uma função com as mesmas ou menos permissões do que sua própria função.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções atribuídas aos usuários

As funções não podem ser alteradas depois que são atribuídas. Para alterar a função atribuída a um usuário, exclua o usuário e, em seguida, adicione o usuário novamente com uma função diferente.

> [!NOTE]
> As funções atribuídas são específicas para IoT Central aplicativo e não podem ser gerenciadas no portal do Azure.

## <a name="delete-users"></a>Excluir usuários

Para excluir usuários, marque uma ou mais caixas de seleção na página **usuários** . Em seguida, selecione **Eliminar**.

## <a name="manage-roles"></a>Gerenciar funções

As funções permitem que você controle quem dentro de sua organização tem permissão para realizar várias tarefas no IoT Central. Há três funções internas que você pode atribuir a usuários do seu aplicativo. Você também pode [criar funções personalizadas](#create-a-custom-role) se precisar de um controle mais refinado.

> [!div class="mx-imgBorder"]
> ![a seleção gerenciar funções](media/howto-manage-users-roles-pnp/manage-roles-pnp.png)

### <a name="administrator"></a>Administrador

Os usuários na função **administrador** podem gerenciar e controlar todas as partes do aplicativo, incluindo a cobrança.

O usuário que cria um aplicativo é atribuído automaticamente à função de **administrador** . Sempre deve haver pelo menos um usuário na função de **administrador** .

### <a name="builder"></a>Construtor

Os usuários na função de **Construtor** podem gerenciar todas as partes do aplicativo, mas não podem fazer alterações nas guias administração ou exportação de dados contínuas.

### <a name="operator"></a>Operador

Os usuários na função **operador** podem monitorar a integridade e o status do dispositivo. Eles não podem fazer alterações nos modelos de dispositivo ou administrar o aplicativo. Os operadores podem adicionar e excluir dispositivos, gerenciar conjuntos de dispositivos e executar análises e trabalhos. 

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Se sua solução exigir controles de acesso mais refinados, você poderá criar funções personalizadas com conjuntos personalizados de permissões. Para criar uma função personalizada, navegue até a página **funções** na seção **Administração** do seu aplicativo. Em seguida, selecione **+ nova função**e adicione um nome e uma descrição para sua função. Selecione as permissões que sua função requer e, em seguida, selecione **salvar**.

Você pode adicionar usuários à sua função personalizada da mesma maneira que adiciona usuários a uma função interna.

> [!div class="mx-imgBorder"]
> ![criar uma função personalizada](media/howto-manage-users-roles-pnp/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opções de função personalizadas

Ao definir uma função personalizada, você escolhe o conjunto de permissões que um usuário recebe se ele for um membro da função. Algumas permissões dependem de outras. Por exemplo, se você adicionar a permissão **Atualizar painéis do aplicativo** a uma função, a permissão **Exibir painéis do aplicativo** será adicionada automaticamente. As tabelas a seguir resumem as permissões disponíveis e suas dependências, que você pode usar ao criar funções personalizadas.

#### <a name="managing-devices"></a>Gerenciando dispositivos

**Permissões de modelo de dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum     |
| Gerir | Ver <br/> Outras dependências: exibir instâncias de dispositivo  |
| Controle total | Exibir, gerenciar <br/> Outras dependências: exibir instâncias de dispositivo |

**Permissões de instância de dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos |
| Atualizar | Ver <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |
| Criar | Ver <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |
| Eliminar | Ver <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |
| Executar comandos | Atualizar, exibir <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |
| Controle total | Exibir, atualizar, criar, excluir, executar comandos <br/> Outras dependências: exibir modelos de dispositivo e grupos de dispositivos  |

**Permissões de grupos de dispositivos**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo |
| Atualizar | Ver <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo   |
| Criar | Exibir, atualizar <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo   |
| Eliminar | Ver <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo   |
| Controle total | Exibir, atualizar, criar, excluir <br/> Outras dependências: exibir modelos de dispositivo e instâncias de dispositivo |

**Permissões de gerenciamento de conectividade do dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Ler instância | Nenhum <br/> Outras dependências: exibir modelos de dispositivo, grupos de dispositivos, instâncias de dispositivo |
| Gerenciar instância | Nenhum |
| Ler global | Nenhum   |
| Gerenciar global | Ler global |
| Controle total | Ler instância, gerenciar instância, ler global, gerenciar global. <br/> Outras dependências: exibir modelos de dispositivo, grupos de dispositivos, instâncias de dispositivo |

**Permissões de trabalhos**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos |
| Atualizar | Ver <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos |
| Criar | Exibir, atualizar <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos |
| Eliminar | Ver <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos |
| Executar | Ver <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos; Atualizar instâncias de dispositivo; Executar comandos em instâncias de dispositivo |
| Controle total | Exibir, atualizar, criar, excluir, executar <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo e grupos de dispositivos; Atualizar instâncias de dispositivo; Executar comandos em instâncias de dispositivo |

**Permissões de regras**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum <br/> Outras dependências: exibir modelos de dispositivo |
| Atualizar | Ver <br/> Outras dependências: exibir modelos de dispositivo |
| Criar | Exibir, atualizar <br/> Outras dependências: exibir modelos de dispositivo |
| Eliminar | Ver <br/> Outras dependências: exibir modelos de dispositivo |
| Controle total | Exibir, atualizar, criar, excluir <br/> Outras dependências: exibir modelos de dispositivo |

#### <a name="managing-the-app"></a>Gerenciando o aplicativo

**Permissões de configurações do aplicativo**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum     |
| Atualizar | Ver   |
| Copiar | Ver <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo, grupos de dispositivos, painéis, exportação de dados, identidade visual, links de ajuda, funções personalizadas, regras |
| Eliminar | Ver   |
| Controle total | Exibir, atualizar, copiar, excluir <br/> Outras dependências: exibir modelos de dispositivo, grupos de dispositivos, painéis de aplicativos, exportação de dados, identidade visual, links de ajuda, funções personalizadas, regras |

**Permissões de exportação de modelo de aplicativo**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum     |
| Exportar | Ver <br/> Outras dependências: exibir modelos de dispositivo, instâncias de dispositivo, grupos de dispositivos, painéis, exportação de dados, identidade visual, links de ajuda, funções personalizadas, regras |
| Controle total | Exibir, exportar <br/> Outras dependências: exibir modelos de dispositivo, grupos de dispositivos, painéis de aplicativos, exportação de dados, identidade visual, links de ajuda, funções personalizadas, regras |

**Permissões de cobrança**

| Nome | Dependências |
| ---- | -------- |
| Gerir | Nenhum     |
| Controle total | Gerir |

#### <a name="managing-users-and-roles"></a>Gerenciando usuários e funções

**Permissões de funções personalizadas**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum |
| Atualizar | Ver |
| Criar | Exibir, atualizar |
| Eliminar | Ver |
| Controle total | Exibir, atualizar, criar, excluir |

**Permissões de gerenciamento de usuário**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum <br/> Outras dependências: exibir funções personalizadas |
| Adicionar | Ver <br/> Outras dependências: exibir funções personalizadas |
| Eliminar | Ver <br/> Outras dependências: exibir funções personalizadas |
| Controle total | Exibir, adicionar, excluir <br/> Outras dependências: exibir funções personalizadas |

> [!NOTE]
> Um usuário que está em uma função personalizada que concede a eles a permissão para adicionar outros usuários só pode adicionar usuários a uma função com as mesmas ou menos permissões do que sua própria função.

#### <a name="customizing-the-app"></a>Personalizando o aplicativo

**Permissões do painel de aplicativo**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum     |
| Atualizar | Ver   |
| Criar | Exibir, atualizar |
| Eliminar | Ver   |
| Controle total | Exibir, atualizar, criar, excluir |

**Permissões de painéis pessoais**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum     |
| Atualizar | Ver   |
| Criar | Exibir, atualizar   |
| Eliminar | Ver   |
| Controle total | Exibir, atualizar, criar, excluir |

**Permissões de identidade visual, favicon e cores**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum     |
| Atualizar | Ver   |
| Controle total | Exibir, atualizar |

**Permissões de links de ajuda**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum     |
| Atualizar | Ver   |
| Controle total | Exibir, atualizar |

#### <a name="extending-the-app"></a>Estendendo o aplicativo

**Permissões de exportação de dados**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum     |
| Atualizar | Ver   |
| Criar | Exibir, atualizar  |
| Eliminar | Ver   |
| Controle total | Exibir, atualizar, criar, excluir |

**Permissões de token de API**

| Nome | Dependências |
| ---- | -------- |
| Ver | Nenhum     |
| Criar | Ver   |
| Eliminar | Ver   |
| Controle total | Exibir, criar, excluir |

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre como gerenciar usuários e funções em seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar sua fatura](howto-view-bill-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
