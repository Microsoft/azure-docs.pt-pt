---
title: Modo de dispositivo partilhado para dispositivos Android
titleSuffix: Microsoft identity platform | Azure
description: Saiba como ativar o modo de dispositivo partilhado para permitir que os Trabalhadores da Firstline partilhem um dispositivo Android
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: d9874e27c21906512c2f6c841767b4d6591dbeaf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550269"
---
# <a name="shared-device-mode-for-android-devices"></a>Modo de dispositivo partilhado para dispositivos Android

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Os trabalhadores da Firstline, como os associados de retalho, os tripulantes de voo e os trabalhadores do serviço de campo, usam frequentemente um dispositivo móvel partilhado para fazer o seu trabalho. Isso torna-se problemático quando começam a partilhar palavras-passe ou números de pin para aceder aos dados dos clientes e negócios no dispositivo partilhado.

O modo de dispositivo partilhado permite configurar um dispositivo Android para que possa ser facilmente partilhado por vários colaboradores. Os colaboradores podem iniciar sessão e aceder rapidamente à informação do cliente. Quando terminarem o seu turno ou tarefa, podem assinar fora do dispositivo e estará imediatamente pronto para o próximo funcionário utilizar.

O modo de dispositivo partilhado também fornece a gestão apoiada pela identidade da Microsoft do dispositivo.

Para criar uma aplicação de modo de dispositivo partilhado, os desenvolvedores e os administradores de dispositivos de nuvem trabalham em conjunto:

- Os desenvolvedores escrevem uma aplicação de uma única conta (as `"shared_device_mode_supported": true` aplicações de várias contas não são suportadas no modo de dispositivo partilhado), adicionam à configuração da aplicação e escrevem código para lidar com coisas como o sign-out de dispositivos partilhados.
- Os administradores do dispositivo preparam o dispositivo para ser partilhado instalando a aplicação do autenticador e definindo o dispositivo para o modo partilhado utilizando a aplicação autenticadora. Apenas os utilizadores que se encontram na função [cloud device administrator](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator-permissions) podem colocar um dispositivo no modo partilhado utilizando a [aplicação Authenticator](../user-help/user-help-auth-app-overview.md). Pode configurar a adesão das suas funções organizacionais no portal Azure através de: **Azure Ative Directory** > **Roles and Administrators** > Cloud Device**Administrator**.

 Este artigo centra-se principalmente no que os desenvolvedores devem pensar.

## <a name="single-vs-multiple-account-applications"></a>Aplicações individuais vs múltiplas conta

As aplicações escritas utilizando o Microsoft Authentication Library SDK (MSAL) podem gerir uma única conta ou várias contas. Para mais detalhes, consulte o [modo de uma única conta ou o modo de conta múltipla](single-multi-account.md). As funcionalidades da plataforma de identidade da Microsoft disponíveis para a sua aplicação variam consoante a aplicação esteja a funcionar no modo de uma única conta ou no modo de várias contas.

**As aplicações de modo de dispositivo partilhado funcionam apenas no modo de uma única conta.**

> [!IMPORTANT]
> As aplicações que suportam apenas o modo de várias contas não podem ser executadas num dispositivo partilhado. Se um funcionário carregar uma aplicação que não suporta o modo de conta única, não funcionará no dispositivo partilhado.
>
> As aplicações escritas antes do SDK MSAL foram lançadas executadas no modo de várias contas e devem ser atualizadas para suportar o modo de conta única antes de poderem ser executadas num dispositivo de modo partilhado.

**Apoio tanto de conta única como de múltiplas contas**

A sua aplicação pode ser construída para suportar o funcionamento tanto em dispositivos pessoais como em dispositivos partilhados. Se a sua aplicação suporta atualmente várias contas e pretende suportar o modo de dispositivo partilhado, adicione suporte para o modo de conta única.

Também pode querer que a sua aplicação altere o seu comportamento dependendo do tipo de dispositivo em que está a funcionar. Utilize `ISingleAccountPublicClientApplication.isSharedDevice()` para determinar quando correr no modo de uma única conta.

Existem duas interfaces diferentes que representam o tipo de dispositivo em que a sua aplicação está. Quando solicita uma instância de candidatura à fábrica de aplicação da MSAL, o objeto de aplicação correto é fornecido automaticamente.

O seguinte modelo de objeto ilustra o tipo de objeto que pode receber e o que significa no contexto de um dispositivo partilhado:

![modelo de herança de pedido de cliente público](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Terá de fazer uma verificação de tipo e lançar `PublicClientApplication` para a interface apropriada quando tiver o seu objeto. O seguinte código verifica o modo de conta múltipla ou o modo de conta única, e lança o objeto de aplicação adequadamente:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

As seguintes diferenças aplicam-se dependendo se a sua aplicação está a funcionar num dispositivo partilhado ou pessoal:

|  | Dispositivo de modo compartilhado  | Dispositivo pessoal |
|---------|---------|---------|
| **Contas**     | Conta única | Múltiplas contas |
| **Iniciar sessão** | Global | Global |
| **Terminar sessão** | Global | Cada aplicação pode controlar se a inscrição é local para a app ou para a família de aplicações. |
| **Tipos de conta suportados** | Contas de trabalho apenas | Contas pessoais e de trabalho suportadas  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Por que você pode querer apenas suportar o modo de conta única

Se estiver a escrever uma aplicação que só será utilizada para trabalhadores de primeira linha utilizando um dispositivo partilhado, recomendamos que escreva a sua aplicação apenas para suportar o modo de conta única. Isto inclui a maioria das aplicações focadas em tarefas, tais como aplicações de registos médicos, aplicações de faturas e a maioria das aplicações de linha de negócio. Apenas apoiar o modo de conta única simplifica o desenvolvimento porque você não precisará implementar as funcionalidades adicionais que fazem parte de aplicações de várias contas.

## <a name="what-happens-when-the-device-mode-changes"></a>O que acontece quando o modo do dispositivo muda

Se a sua aplicação estiver em execução no modo de várias contas, e um administrador colocar o dispositivo no modo de dispositivo partilhado, todas as contas do dispositivo são retiradas da aplicação e as transições da aplicação para o modo de conta única.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Inscrição de dispositivo compartilhado e o ciclo de vida geral da aplicação

Quando um utilizador se inscreve, terá de tomar medidas para proteger a privacidade e os dados do utilizador. Por exemplo, se estiver a construir uma aplicação de registos médicos, vai querer certificar-se de que quando o utilizador assinar os registos do paciente previamente apresentados são limpos. A sua candidatura deve estar preparada para isso e verificar sempre que entrar em primeiro plano.

Quando a sua aplicação utiliza o MSAL para assinar o utilizador numa aplicação em execução no dispositivo que se encontra em modo partilhado, a conta de inscrição e os tokens em cache são removidos tanto da aplicação como do dispositivo.

O diagrama seguinte mostra o ciclo de vida geral da aplicação e eventos comuns que podem ocorrer durante o funcionao da sua aplicação. O diagrama cobre a partir do momento em que uma atividade é lançada, iniciando e assinando uma conta, e como eventos como parar, retomar e parar a atividade se encaixam.

![Ciclo de vida da aplicação de dispositivo sintetício](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Passos seguintes

Experimente o [modo use de dispositivo partilhado no tutorial de aplicações Android](tutorial-v2-shared-device-mode.md) que mostra como executar uma aplicação de trabalho de primeira linha num dispositivo Android de modo partilhado.
