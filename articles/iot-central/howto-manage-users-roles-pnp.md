---
title: Gerenciar usuários e funções no aplicativo IoT Central do Azure | Microsoft Docs
description: Como administrador, como gerenciar usuários e funções no aplicativo IoT Central do Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1a6b7370a4d1f8e01b22bfd52caa6cb6973947
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880647"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gerenciar usuários e funções em seu aplicativo IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Este artigo descreve como, como administrador, você pode adicionar, editar e excluir usuários em seu aplicativo de IoT Central do Azure e também como gerenciar funções em seu aplicativo de IoT Central do Azure.

Para acessar e usar a seção **Administração** , você deve estar na função **administrador** para um aplicativo IOT central do Azure. Se você criar um aplicativo de IoT Central do Azure, você será automaticamente atribuído à função de **administrador** para esse aplicativo.


## <a name="add-users"></a>Adicionar utilizadores

Cada usuário deve ter uma conta de usuário antes que possa entrar e acessar um aplicativo de IoT Central do Azure. As contas da Microsoft (MSAs) e Azure Active Directory (Azure AD) têm suporte no IoT Central do Azure. Atualmente, não há suporte para grupos de Azure Active Directory no IoT Central do Azure.

Para obter mais informações, consulte [conta Microsoft ajuda](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [início rápido: Adicione novos usuários a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para adicionar um usuário a um aplicativo IoT Central, vá para a página **usuários** na seção **Administração** .

    ![Lista de utilizadores](media/howto-manage-users-roles-pnp/image1.png)

1. Para adicionar um usuário, na página **usuários** , escolha **+ Adicionar usuário**.

1. Escolha uma função para o usuário no menu suspenso **função** . Saiba mais sobre as funções na seção [gerenciar funções](#manage-roles) deste artigo.

    ![Seleção de função](media/howto-manage-users-roles-pnp/image3.png)

    > [!NOTE]
    >  Para adicionar usuários em massa, insira as IDs de usuário de todos os usuários que você gostaria de adicionar separados por ponto e vírgula. Escolha uma função no menu suspenso **função** . Em seguida, selecione **Guardar**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções atribuídas aos usuários

As funções não podem ser alteradas depois de serem atribuídas. Para alterar a função atribuída a um usuário, exclua o usuário e, em seguida, adicione o usuário novamente com uma função diferente.

> [!NOTE]
> As funções atribuídas são específicas para IoT Central aplicativo e não podem ser gerenciadas no portal do Azure.

## <a name="delete-users"></a>Excluir usuários

Para excluir usuários, marque uma ou mais caixas de seleção na página **usuários** . Em seguida, selecione **Eliminar**.

## <a name="manage-roles"></a>Gerenciar funções

As funções permitem que você controle quem dentro de sua organização pode executar várias tarefas no IoT Central. Há três funções que você pode atribuir a usuários do seu aplicativo.

### <a name="administrator"></a>Admistrador

Os usuários na função de **administrador** têm acesso a todas as funcionalidades em um aplicativo.

O usuário que cria um aplicativo é atribuído automaticamente à função de **administrador** . Sempre deve haver pelo menos um usuário na função de **administrador** .

### <a name="application-builder"></a>Construtor de Aplicações

Os usuários na função do **Application Builder** podem fazer tudo em um aplicativo, exceto administrar o aplicativo. Os criadores podem criar, editar e excluir modelos de dispositivo e dispositivos, gerenciar conjuntos de dispositivos e executar análises e trabalhos. Os construtores não terão acesso à seção **Administração** do aplicativo.

### <a name="application-operator"></a>Operador de Aplicações

Os usuários na função **operador de aplicativo** não podem fazer alterações nos modelos de dispositivo e não podem administrar o aplicativo. Os operadores podem adicionar e excluir dispositivos, gerenciar conjuntos de dispositivos e executar análises e trabalhos. Os operadores não terão acesso ao **Application Builder** e às páginas de **Administração** .

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu sobre como gerenciar usuários e funções em sua IoT Central do Azure, a próxima etapa sugerida é saber mais sobre como [exibir sua fatura](howto-view-bill-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) no Azure IOT central.
