---
title: Gerir utilizadores e funções na aplicação Azure IoT Central | Microsoft Docs
description: Como administrador, como gerir utilizadores e funções na sua aplicação Azure IoT Central
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 928a9fbad7bde7fe7f1cfaf181bff2a7b9ce458b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599065"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gerir utilizadores e funções na aplicação IoT Central

Este artigo descreve como, como administrador, pode adicionar, editar e eliminar utilizadores na sua aplicação Azure IoT Central. O artigo também descreve como gerir funções na sua aplicação.

## <a name="add-users"></a>Adicionar utilizadores

Todos os utilizadores devem ter uma conta de utilizador antes de poderem iniciar sôms e aceder a uma aplicação. A IoT Central suporta atualmente contas da Microsoft e contas de Diretório Ativo Azure, mas não grupos de Diretório Ativo Azure.

Para obter mais informações, consulte [a ajuda da conta da Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e  [quickstart: Adicione novos utilizadores ao Azure Ative Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Para adicionar um utilizador a uma aplicação IoT Central, aceda à página **de Utilizadores** na secção **Administração.**
    
    > [!div class="mx-imgBorder"]
    >![Gerir utilizadores](media/howto-manage-users-roles/manage-users-pnp.png)

1. Para adicionar um utilizador, na página **do Utilizador,** escolha **+ Adicionar utilizador**.

1. Escolha uma função para o utilizador **a** partir do menu Role drop-down. Saiba mais sobre os papéis na secção [Gerir os papéis](#manage-roles) deste artigo.

    > [!div class="mx-imgBorder"]
    >![Adicione o utilizador e selecione uma função](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Um utilizador que esteja numa função personalizada que lhes conceda a permissão para adicionar outros utilizadores, só pode adicionar utilizadores a uma função com as mesmas ou menos permissões do que o seu próprio papel.
    > 
    > Se um utilizador for eliminado do Azure Ative Directory e depois adicionado, não poderá assinar automaticamente a aplicação IoT Central. Para voltar a ativar o acesso, o administrador da aplicação deve eliminar e voltar a adicionar o utilizador na aplicação.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções que são atribuídas aos utilizadores

Os papéis não podem ser alterados depois de serem atribuídos. Para alterar a função atribuída a um utilizador, elimine o utilizador e, em seguida, adicione o utilizador novamente com uma função diferente.

> [!NOTE]
> As funções atribuídas são específicas da aplicação IoT Central e não podem ser geridas a partir do Portal Azure.

## <a name="delete-users"></a>Eliminar utilizadores

Para eliminar os utilizadores, selecione uma ou mais caixas de verificação na página **do Utilizadores.** Em seguida, selecione **Eliminar**.

## <a name="manage-roles"></a>Gerir funções

As funções permitem-lhe controlar quem dentro da sua organização está autorizado a fazer várias tarefas na IoT Central. Existem três funções incorporadas que pode atribuir aos utilizadores da sua aplicação. Também pode [criar funções personalizadas](#create-a-custom-role) se necessitar de um controlo mais fino.

> [!div class="mx-imgBorder"]
> ![Gerir a seleção de funções](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administrador

Os utilizadores na função **administrador** podem gerir e controlar todas as partes da aplicação, incluindo a faturação.

O utilizador que cria uma aplicação é automaticamente atribuído à função **Administrador.** Deve haver sempre pelo menos um utilizador na função **de Administrador.**

### <a name="builder"></a>Construtor

Os utilizadores na função **Builder** podem gerir todas as partes da app, mas não podem fazer alterações nos separadores de Administração ou Exportação contínua de dados.

### <a name="operator"></a>Operador

Os utilizadores da função **'Operador'** podem monitorizar a saúde e o estado do dispositivo. Não estão autorizados a fazer alterações nos modelos do dispositivo ou a administrar a aplicação. Os operadores podem adicionar e eliminar dispositivos, gerir conjuntos de dispositivos e executar análises e empregos. 

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Se a sua solução necessitar de controlos de acesso com grãos mais finos, pode criar funções com conjuntos de permissões personalizados. Para criar um papel personalizado, navegue para a página **Roles** na secção **Administração** da sua aplicação. Em seguida, selecione **+ Nova função**, e adicione um nome e descrição para o seu papel. Selecione as permissões que a sua função requer e, em seguida, **selecione Guardar**.

Pode adicionar os utilizadores ao seu papel personalizado da mesma forma que adiciona os utilizadores a um papel incorporado.

> [!div class="mx-imgBorder"]
> ![Construir um papel personalizado](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opções de função personalizadas

Quando define um papel personalizado, escolhe o conjunto de permissões que um utilizador é concedido se for membro do papel. Algumas permissões dependem de outras. Por exemplo, se adicionar a permissão **de dashboards de aplicação Update** a uma função, também precisa da permissão **dos dashboards da aplicação Ver.** As tabelas que se seguem resumem as permissões disponíveis e as suas dependências, pode utilizar ao criar funções personalizadas.

#### <a name="managing-devices"></a>Gestão de dispositivos

**Permissões do modelo do dispositivo**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum     |
| Gerir | Vista <br/> Outras dependências: Ver instâncias do dispositivo  |
| Controlo Total | Ver, Gerir <br/> Outras dependências: Ver instâncias do dispositivo |

**Permissões de instância do dispositivo**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos |
| Atualizar | Vista <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |
| Criar | Vista <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |
| Eliminar | Vista <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |
| Executar comandos | Atualizar, Ver <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |
| Ver dados brutos | Vista <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |
| Controlo Total | Ver, Atualizar, Criar, Eliminar, Executar comandos, Ver dados brutos <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |

**Permissões de grupos de dispositivos**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivo |
| Atualizar | Vista <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivo   |
| Criar | Ver, Atualizar <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivo   |
| Eliminar | Vista <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivo  |
| Controlo Total | Ver, Atualizar, Criar, Eliminar <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivo |

**Permissões de gestão da conectividade do dispositivo**

| Name | Dependências |
| ---- | -------- |
| Ler caso | Nenhum <br/> Outras dependências: Ver modelos de dispositivos, grupos de dispositivos, instâncias de dispositivo |
| Gerir instância | Ler caso <br /> Outras dependências: Ver modelos de dispositivos, grupos de dispositivos, instâncias de dispositivo |
| Ler global | Nenhum   |
| Gerir globalmente | Ler global |
| Controlo Total | Ler exemplo, gerir exemplos, ler globalmente, gerir globalmente <br/> Outras dependências: Ver modelos de dispositivos, grupos de dispositivos, instâncias de dispositivo |

**Permissões de emprego**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Atualizar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Criar | Ver, Atualizar <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Eliminar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Executar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos; Atualização de instâncias do dispositivo; Executar comandos em instâncias de dispositivos |
| Controlo Total | Ver, Atualizar, Criar, Eliminar, Executar <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos; Atualização de instâncias do dispositivo; Executar comandos em instâncias de dispositivos |

**Permissões de regras**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum <br/> Outras dependências: Ver modelos de dispositivos |
| Atualizar | Vista <br/> Outras dependências: Ver modelos de dispositivos |
| Criar | Ver, Atualizar <br/> Outras dependências: Ver modelos de dispositivos |
| Eliminar | Vista <br/> Outras dependências: Ver modelos de dispositivos |
| Controlo Total | Ver, Atualizar, Criar, Eliminar <br/> Outras dependências: Ver modelos de dispositivos |

#### <a name="managing-the-app"></a>Gestão da app

**Permissões de definições de aplicações**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum     |
| Atualizar | Vista   |
| Copiar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos, grupos de dispositivos, dashboards, exportação de dados, marcação, links de ajuda, funções personalizadas, regras |
| Eliminar | Vista   |
| Controlo Total | Ver, Atualizar, Copiar, Eliminar <br/> Outras dependências: Ver modelos de dispositivos, grupos de dispositivos, dashboards de aplicações, exportação de dados, marcação, links de ajuda, funções personalizadas, regras |

**Permissões de exportação de modelo de aplicação**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum     |
| Exportar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos, grupos de dispositivos, dashboards, exportação de dados, marcação, links de ajuda, funções personalizadas, regras |
| Controlo Total | Vista, Exportação <br/> Outras dependências: Ver modelos de dispositivos, grupos de dispositivos, dashboards de aplicações, exportação de dados, marcação, links de ajuda, funções personalizadas, regras |

**Permissões de upload de ficheiros de dispositivo**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum     |
| Gerir | Vista   |
| Controlo Total | Ver, Gerir |

**Permissões de faturação**

| Name | Dependências |
| ---- | -------- |
| Gerir | Nenhum     |
| Controlo Total | Gerir |

#### <a name="managing-users-and-roles"></a>Gestão de utilizadores e funções

**Permissões de funções personalizadas**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum |
| Atualizar | Vista |
| Criar | Ver, Atualizar |
| Eliminar | Vista |
| Controlo Total | Ver, Atualizar, Criar, Eliminar |

**Permissões de gestão de utilizadores**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum <br/> Outras dependências: Ver funções personalizadas |
| Adicionar | Vista <br/> Outras dependências: Ver funções personalizadas |
| Eliminar | Vista <br/> Outras dependências: Ver funções personalizadas |
| Controlo Total | Ver, Adicionar, Excluir <br/> Outras dependências: Ver funções personalizadas |

> [!NOTE]
> Um utilizador que esteja numa função personalizada que lhes conceda a permissão para adicionar outros utilizadores, só pode adicionar utilizadores a uma função com as mesmas ou menos permissões do que o seu próprio papel.

#### <a name="customizing-the-app"></a>Personalizar a app

**Permissões do painel de aplicações**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum     |
| Atualizar | Vista   |
| Criar | Ver, Atualizar |
| Eliminar | Vista   |
| Controlo Total | Ver, Atualizar, Criar, Eliminar |

**Permissões de painéis pessoais**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum     |
| Atualizar | Vista   |
| Criar | Ver, Atualizar   |
| Eliminar | Vista   |
| Controlo Total | Ver, Atualizar, Criar, Eliminar |

**Permissões de marcação, favicon e cores**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum     |
| Atualizar | Vista   |
| Controlo Total | Ver, Atualizar |

**Permissões de links de ajuda**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum     |
| Atualizar | Vista   |
| Controlo Total | Ver, Atualizar |

#### <a name="extending-the-app"></a>Ampliação da app

**Permissões de exportação de dados**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum     |
| Atualizar | Vista   |
| Criar | Ver, Atualizar  |
| Eliminar | Vista   |
| Controlo Total | Ver, Atualizar, Criar, Eliminar |

**Permissões simbólicas da API**

| Name | Dependências |
| ---- | -------- |
| Vista | Nenhum  <br/> Outras dependências: Ver funções personalizadas |
| Criar | Vista <br/> Outras dependências: Ver funções personalizadas |
| Eliminar | Vista <br/> Outras dependências: Ver funções personalizadas |
| Controlo Total | Ver, Criar, Eliminar <br/> Outras dependências: Ver funções personalizadas |

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir utilizadores e papéis na sua aplicação IoT Central, o próximo passo sugerido é aprender a gerir a [sua conta.](howto-view-bill.md)