---
title: Gerir utilizadores e funções na aplicação Azure IoT Central [ Microsoft Docs
description: Como administrador, como gerir utilizadores e funções na sua aplicação Azure IoT Central
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c00f9d8baa55ef0d0cf6322ee71f22e739e6acdc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80365503"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gerir utilizadores e funções na sua aplicação IoT Central

Este artigo descreve como, como administrador, pode adicionar, editar e excluir utilizadores na sua aplicação Azure IoT Central. O artigo também descreve como gerir papéis na sua aplicação Azure IoT Central.

Para aceder e utilizar a secção **Administração,** deve estar na função **de Administrador** para uma aplicação Azure IoT Central. Se criar uma aplicação Azure IoT Central, é automaticamente adicionado ao papel de **Administrador** para essa aplicação.

## <a name="add-users"></a>Adicionar utilizadores

Todos os utilizadores devem ter uma conta de utilizador antes de poderem iniciar sessão e aceder a uma aplicação Azure IoT Central. As contas Microsoft Accounts e Azure Ative Directory são suportadas no Azure IoT Central. Os grupos de Diretórios Ativos da Azure não são atualmente apoiados na Azure IoT Central.

Para mais informações, consulte [a ajuda da conta da Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [quickstart: Adicione novos utilizadores ao Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para adicionar um utilizador a uma aplicação IoT Central, aceda à página **de Utilizadores** na secção **Administração.**
    
    > [!div class="mx-imgBorder"]
    >![Gerir utilizadores](media/howto-manage-users-roles/manage-users-pnp.png)

1. Para adicionar um utilizador, na página **utilizadores,** escolha **+ Adicionar utilizador**.

1. Escolha uma função para o utilizador a partir do menu de entrega de **funções.** Saiba mais sobre os papéis na secção [de gestão](#manage-roles) de papéis deste artigo.

    > [!div class="mx-imgBorder"]
    >![Adicione o utilizador e selecione uma função](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Um utilizador que esteja numa função personalizada que lhes conceda a permissão para adicionar outros utilizadores, só pode adicionar utilizadores a um papel com permissões iguais ou menos do que a sua própria função.

Se um ID de utilizador IoT Central for eliminado do Diretório Ativo Do Azure e depois adicionado, o utilizador não poderá assinar na aplicação IoT Central. Para reativar o acesso, o administrador da IoT Central deve eliminar e ler o utilizador na aplicação.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções atribuídas aos utilizadores

Os papéis não podem ser mudados depois de serem designados. Para alterar a função atribuída a um utilizador, elimine o utilizador e adicione novamente o utilizador com uma função diferente.

> [!NOTE]
> As funções atribuídas são específicas da aplicação IoT Central e não podem ser geridas a partir do Portal Azure.

## <a name="delete-users"></a>Eliminar utilizadores

Para eliminar os utilizadores, selecione uma ou mais caixas de verificação na página **utilizadores.** Em seguida, selecione **Eliminar**.

## <a name="manage-roles"></a>Gerir funções

As funções permitem controlar quem dentro da sua organização está autorizado a fazer várias tarefas na IoT Central. Existem três funções incorporadas que pode atribuir aos utilizadores da sua aplicação. Também pode [criar papéis personalizados](#create-a-custom-role) se necessitar de um controlo mais fino.

> [!div class="mx-imgBorder"]
> ![Gerir a seleção de papéis](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administrador

Os utilizadores na função **De Administrador** podem gerir e controlar todas as partes da aplicação, incluindo a faturação.

O utilizador que cria uma aplicação é automaticamente atribuído à função **De Administrador.** Deve haver sempre pelo menos um utilizador na função **de Administrador.**

### <a name="builder"></a>Construtor

Os utilizadores na função **Construtora** podem gerir todas as partes da app, mas não podem fazer alterações nos separadores de Exportação de Dados Contínuos ou Administração ou Dedados Contínuos.

### <a name="operator"></a>Operador

Os utilizadores na função **Operador** a ver a saúde e o estado do dispositivo podem monitorizar a saúde e o estado do dispositivo. Não estão autorizados a fazer alterações nos modelos do dispositivo ou a administrar a aplicação. Os operadores podem adicionar e apagar dispositivos, gerir conjuntos de dispositivos e executar análises e empregos. 

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Se a sua solução necessitar de controlos de acesso mais finos, pode criar funções personalizadas com conjuntos personalizados de permissões. Para criar uma função personalizada, navegue para a página **De Funções** na secção **Administração** da sua aplicação. Em seguida, selecione **+ Nova função,** e adicione um nome e descrição para o seu papel. Selecione as permissões que a sua função necessita e, em seguida, selecione **Guardar**.

Pode adicionar os utilizadores ao seu papel personalizado da mesma forma que adiciona os utilizadores a uma função incorporada.

> [!div class="mx-imgBorder"]
> ![Construir um papel personalizado](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opções de role personalizados

Quando define um papel personalizado, escolhe o conjunto de permissões que um utilizador é concedido se for membro do papel. Algumas permissões dependem de outras. Por exemplo, se adicionar a permissão dos dashboards de **aplicação Update** a uma função, a permissão de dashboards de **aplicação View** é adicionada automaticamente. As tabelas seguintes resumem as permissões disponíveis e as suas dependências, pode usar na criação de papéis personalizados.

#### <a name="managing-devices"></a>Dispositivos de gestão

**Permissões do modelo do dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma     |
| Gerir | Vista <br/> Outras dependências: Ver instâncias de dispositivos  |
| Controlo Total | Ver, Gerir <br/> Outras dependências: Ver instâncias de dispositivos |

**Permissões de instância do dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos |
| Atualizar | Vista <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |
| Criar | Vista <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |
| Eliminar | Vista <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |
| Executar Comandos | Atualização, Visualização <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |
| Controlo Total | Ver, Atualizar, Criar, Excluir, Executar Comandos <br/> Outras dependências: Ver modelos de dispositivos e grupos de dispositivos  |

**Permissões de grupos de dispositivos**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivos |
| Atualizar | Vista <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivos   |
| Criar | Ver, Atualizar <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivos   |
| Eliminar | Vista <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivos   |
| Controlo Total | Ver, Atualizar, Criar, Excluir <br/> Outras dependências: Ver modelos de dispositivos e instâncias de dispositivos |

**Permissões de gestão da conectividade do dispositivo**

| Nome | Dependências |
| ---- | -------- |
| Leia a instância | Nenhuma <br/> Outras dependências: Ver modelos de dispositivos, grupos de dispositivos, instâncias de dispositivos |
| Gerir instância | Nenhuma |
| Ler global | Nenhuma   |
| Gerir globalmente | Ler Global |
| Controlo Total | Ler exemplo, Gerir instância, ler global, gerir globalmente. <br/> Outras dependências: Ver modelos de dispositivos, grupos de dispositivos, instâncias de dispositivos |

**Permissões de emprego**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Atualizar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Criar | Ver, Atualizar <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Eliminar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos |
| Executar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos; Atualizar casos de dispositivos; Executar comandos em instâncias de dispositivos |
| Controlo Total | Ver, Atualizar, Criar, Excluir, Executar <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos e grupos de dispositivos; Atualizar casos de dispositivos; Executar comandos em instâncias de dispositivos |

**Permissões de regras**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma <br/> Outras dependências: Ver modelos de dispositivo |
| Atualizar | Vista <br/> Outras dependências: Ver modelos de dispositivo |
| Criar | Ver, Atualizar <br/> Outras dependências: Ver modelos de dispositivo |
| Eliminar | Vista <br/> Outras dependências: Ver modelos de dispositivo |
| Controlo Total | Ver, Atualizar, Criar, Excluir <br/> Outras dependências: Ver modelos de dispositivo |

#### <a name="managing-the-app"></a>Gestão da app

**Permissões de definições de aplicação**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma     |
| Atualizar | Vista   |
| Copiar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos, grupos de dispositivos, dashboards, exportação de dados, branding, links de ajuda, funções personalizadas, regras |
| Eliminar | Vista   |
| Controlo Total | Ver, Atualizar, Copiar, Excluir <br/> Outras dependências: Ver modelos de dispositivos, grupos de dispositivos, dashboards de aplicações, exportação de dados, branding, ligações de ajuda, papéis personalizados, regras |

**Permissões de exportação de modelo de aplicação**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma     |
| Exportar | Vista <br/> Outras dependências: Ver modelos de dispositivos, instâncias de dispositivos, grupos de dispositivos, dashboards, exportação de dados, branding, links de ajuda, funções personalizadas, regras |
| Controlo Total | Vista, Exportação <br/> Outras dependências: Ver modelos de dispositivos, grupos de dispositivos, dashboards de aplicações, exportação de dados, branding, ligações de ajuda, papéis personalizados, regras |

**Permissões de faturação**

| Nome | Dependências |
| ---- | -------- |
| Gerir | Nenhuma     |
| Controlo Total | Gerir |

#### <a name="managing-users-and-roles"></a>Gestão de utilizadores e funções

**Permissões de papéis personalizados**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma |
| Atualizar | Vista |
| Criar | Ver, Atualizar |
| Eliminar | Vista |
| Controlo Total | Ver, Atualizar, Criar, Excluir |

**Permissões de gestão do utilizador**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma <br/> Outras dependências: Ver papéis personalizados |
| Adicionar | Vista <br/> Outras dependências: Ver papéis personalizados |
| Eliminar | Vista <br/> Outras dependências: Ver papéis personalizados |
| Controlo Total | Ver, Adicionar, Excluir <br/> Outras dependências: Ver papéis personalizados |

> [!NOTE]
> Um utilizador que esteja numa função personalizada que lhes conceda a permissão para adicionar outros utilizadores, só pode adicionar utilizadores a um papel com permissões iguais ou menos do que a sua própria função.

#### <a name="customizing-the-app"></a>Personalizando a app

**Permissões do painel de aplicação**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma     |
| Atualizar | Vista   |
| Criar | Ver, Atualizar |
| Eliminar | Vista   |
| Controlo Total | Ver, Atualizar, Criar, Excluir |

**Permissões de dashboards pessoais**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma     |
| Atualizar | Vista   |
| Criar | Ver, Atualizar   |
| Eliminar | Vista   |
| Controlo Total | Ver, Atualizar, Criar, Excluir |

**Permissões de branding, favicon e cores**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma     |
| Atualizar | Vista   |
| Controlo Total | Ver, Atualizar |

**Ajuda a links permissões**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma     |
| Atualizar | Vista   |
| Controlo Total | Ver, Atualizar |

#### <a name="extending-the-app"></a>Alargar a app

**Permissões de exportação de dados**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma     |
| Atualizar | Vista   |
| Criar | Ver, Atualizar  |
| Eliminar | Vista   |
| Controlo Total | Ver, Atualizar, Criar, Excluir |

**Permissões simbólicas da API**

| Nome | Dependências |
| ---- | -------- |
| Vista | Nenhuma     |
| Criar | Vista   |
| Eliminar | Vista   |
| Controlo Total | Ver, Criar, Excluir |

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir utilizadores e papéis na sua aplicação Azure IoT Central, o próximo passo sugerido é aprender a gerir a [sua conta.](howto-view-bill.md)
