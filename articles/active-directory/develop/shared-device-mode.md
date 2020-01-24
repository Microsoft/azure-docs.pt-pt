---
title: Modo de dispositivo compartilhado para dispositivos Android | Azure
description: Saiba mais sobre o modo de dispositivo compartilhado, que permite que os trabalhadores de primeira a compartilhem um dispositivo Android
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: twhitney
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 53fa82cf6eaaba09353ba21a12ae9677b9264b1a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701472"
---
# <a name="shared-device-mode-for-android-devices"></a>Modo de dispositivo partilhado para dispositivos Android

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os funcionários de primeira a, como Retail Associates, membros da equipe de voo e funcionários de serviço de campo, geralmente usam um dispositivo móvel compartilhado para realizar seu trabalho. Isso se torna problemático quando começa a compartilhar senhas ou números de PIN para acessar dados de clientes e negócios no dispositivo compartilhado.

O modo de dispositivo compartilhado permite que você configure um dispositivo Android para que ele possa ser facilmente compartilhado por vários funcionários. Os funcionários podem entrar e acessar as informações do cliente rapidamente. Quando eles forem concluídos com sua mudança ou tarefa, eles poderão sair do dispositivo e estarão imediatamente prontos para o próximo funcionário usar.

O modo de dispositivo compartilhado também fornece o gerenciamento de Microsoft Identity com suporte do dispositivo.

Para criar um aplicativo de modo de dispositivo compartilhado, os desenvolvedores e os administradores de dispositivo de nuvem funcionam juntos:

- Os desenvolvedores escrevem uma aplicação de uma única conta (as aplicações de várias contas não são suportadas no modo de dispositivo partilhado), adicionam `"shared_device_mode_supported": true` à configuração da aplicação e escrevem código para lidar com coisas como o sign-out de dispositivos partilhados.
- Os administradores de dispositivo preparam o dispositivo a ser compartilhado instalando o aplicativo autenticador e definindo o dispositivo para o modo compartilhado usando o aplicativo autenticador. Somente os usuários que estão na função de [administrador de dispositivo de nuvem](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) podem colocar um dispositivo no modo compartilhado usando o [aplicativo autenticador](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Você pode configurar a associação de suas funções organizacionais no portal do Azure por meio do: **Azure Active Directory** > **funções e administradores** > **administrador de dispositivo de nuvem**.

 Este artigo se concentra principalmente no que os desenvolvedores devem pensar.

## <a name="single-vs-multiple-account-applications"></a>Aplicativos simples versus de várias contas

Os aplicativos escritos usando o SDK da biblioteca de autenticação da Microsoft (MSAL) podem gerenciar uma única conta ou várias contas. Para obter detalhes, consulte modo [de conta única ou modo de conta múltipla](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account). Os recursos da plataforma de identidade da Microsoft disponíveis para seu aplicativo variam dependendo se o aplicativo está sendo executado no modo de conta única ou em modo de várias contas.

Os **aplicativos de modo de dispositivo compartilhado funcionam apenas no modo de conta única**.

> [!IMPORTANT]
> Aplicativos que dão suporte apenas ao modo de várias contas não podem ser executados em um dispositivo compartilhado. Se um funcionário carregar um aplicativo que não dá suporte ao modo de conta única, ele não será executado no dispositivo compartilhado.
>
> Os aplicativos gravados antes do MSAL SDK foi lançado em execução no modo de várias contas e devem ser atualizados para dar suporte ao modo de conta única antes que possam ser executados em um dispositivo de modo compartilhado.

**Suporte a contas única e várias**

Seu aplicativo pode ser criado para dar suporte à execução em dispositivos pessoais e compartilhados. Se seu aplicativo atualmente dá suporte a várias contas e você deseja dar suporte ao modo de dispositivo compartilhado, adicione suporte para o modo de conta única.

Você também pode querer que seu aplicativo altere seu comportamento dependendo do tipo de dispositivo em que ele está sendo executado. Use `ISingleAccountPublicClientApplication.isSharedDevice()` para determinar quando executar no modo de conta única.

Há duas interfaces diferentes que representam o tipo de dispositivo no qual seu aplicativo está. Quando você solicita uma instância de aplicativo do Application Factory da MSAL, o objeto de aplicativo correto é fornecido automaticamente.

O modelo de objeto a seguir ilustra o tipo de objeto que você pode receber e o que significa no contexto de um dispositivo compartilhado:

![modelo de herança de aplicativo cliente público](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Você precisará fazer uma verificação de tipo e convertê-la na interface apropriada ao obter o objeto de `PublicClientApplication`. O código a seguir verifica o modo de várias contas ou o modo de conta única e converte o objeto de aplicativo adequadamente:

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

As seguintes diferenças se aplicam dependendo se seu aplicativo está em execução em um dispositivo compartilhado ou pessoal:

|  | Dispositivo de modo compartilhado  | Dispositivo pessoal |
|---------|---------|---------|
| **Contas**     | Conta única | Várias contas |
| **Iniciar sessão** | Global | Global |
| **Sair** | Global | Cada aplicativo pode controlar se a saída é local para o aplicativo ou para a família de aplicativos. |
| **Tipos de conta com suporte** | Somente contas corporativas | Contas pessoais e corporativas com suporte  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Por que você talvez queira dar suporte apenas ao modo de conta única

Se você estiver escrevendo um aplicativo que será usado apenas para os primeiros trabalhadores que usam um dispositivo compartilhado, recomendamos que você escreva seu aplicativo para dar suporte apenas ao modo de conta única. Isso inclui a maioria dos aplicativos que são focados em tarefas, como os aplicativos de registros médicos, aplicativos de fatura e a maioria dos aplicativos de linha de negócios. Dar suporte apenas ao modo de conta única simplifica o desenvolvimento porque você não precisará implementar os recursos adicionais que fazem parte de aplicativos de várias contas.

## <a name="what-happens-when-the-device-mode-changes"></a>O que acontece quando o modo do dispositivo é alterado

Se seu aplicativo estiver sendo executado no modo de várias contas e um administrador colocar o dispositivo no modo de dispositivo compartilhado, todas as contas no dispositivo serão limpas do aplicativo e o aplicativo passará para o modo de conta única.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Desconexão do dispositivo compartilhado e o ciclo de vida geral do aplicativo

Quando um usuário sair, você precisará tomar medidas para proteger a privacidade e os dados do usuário. Por exemplo, se você estiver criando um aplicativo de registros médicos, certifique-se de que quando o usuário sair dos registros de pacientes exibidos anteriormente sejam limpos. Seu aplicativo deve estar preparado para isso e verificar toda vez que entrar no primeiro plano.

Quando seu aplicativo usa MSAL para desconectar o usuário em um aplicativo em execução no dispositivo que está no modo compartilhado, a conta conectada e os tokens armazenados em cache são removidos do aplicativo e do dispositivo.

O diagrama a seguir mostra o ciclo de vida geral do aplicativo e eventos comuns que podem ocorrer enquanto seu aplicativo é executado. O diagrama abrange desde o momento em que uma atividade é iniciada, entrando e saindo de uma conta e como eventos como pausar, retomar e parar a atividade se ajustam.

![Ciclo de vida do aplicativo de dispositivo compartilhado](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Passos seguintes

Experimente [usar o modo de dispositivo compartilhado no seu aplicativo Android,](tutorial-v2-shared-device-mode.md) que mostra como executar um aplicativo de trabalho de início em um dispositivo Android de modo compartilhado.
