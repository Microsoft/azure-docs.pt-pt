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
ms.openlocfilehash: ea902b8b609d226f6bb9e61ec0bfd60f75a3825d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038582"
---
# <a name="shared-device-mode-for-android-devices"></a>Modo de dispositivo partilhado para dispositivos Android

>[!IMPORTANT]
> Esta característica [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Os trabalhadores da firstline, como os associados de retalho, os tripulantes de voo e os trabalhadores de serviços de campo, usam frequentemente um dispositivo móvel partilhado para fazer o seu trabalho. Isso torna-se problemático quando começam a partilhar palavras-passe ou números pin para aceder a dados de clientes e negócios no dispositivo partilhado.

O modo de dispositivo partilhado permite configurar um dispositivo Android para que possa ser facilmente partilhado por vários funcionários. Os colaboradores podem iniciar seduca e aceder rapidamente à informação do cliente. Quando terminarem com o seu turno ou tarefa, podem assinar fora do dispositivo e estará imediatamente pronto para o próximo funcionário usar.

O modo de dispositivo partilhado também fornece a gestão de identidade da Microsoft do dispositivo.

Para criar uma aplicação de modo de dispositivo compartilhado, os desenvolvedores e os administradores de dispositivos em nuvem trabalham em conjunto:

- Os desenvolvedores escrevem uma aplicação de uma única conta (as aplicações de múltiplas contas não são suportadas no modo de dispositivo partilhado), adicionam `"shared_device_mode_supported": true` à configuração da aplicação e escrevem código para lidar com coisas como a assinatura do dispositivo partilhado.
- Os administradores do dispositivo preparam o dispositivo para ser partilhado instalando a aplicação autenticadora e definindo o dispositivo para o modo partilhado utilizando a aplicação autenticadora. Apenas os utilizadores que estejam na função [de Administrador de Dispositivos cloud](../roles/permissions-reference.md#cloud-device-administrator) podem colocar um dispositivo em modo partilhado utilizando a [aplicação Authenticator](../user-help/user-help-auth-app-overview.md). Pode configurar a adesão aos seus papéis organizacionais no portal Azure via: **Azure Ative Directory**  >  **Roles and Administrators**  >  **Cloud Device Administrators**.

 Este artigo centra-se principalmente no que os desenvolvedores devem pensar.

## <a name="single-vs-multiple-account-applications"></a>Aplicações individuais vs múltiplas contas

As aplicações escritas utilizando a Microsoft Authentication Library SDK (MSAL) podem gerir uma única conta ou várias contas. Para mais informações, consulte [o modo de conta única ou o modo de conta múltipla](single-multi-account.md). As funcionalidades da plataforma de identidade da Microsoft disponíveis para a sua aplicação variam consoante a aplicação esteja a ser executada no modo de conta única ou em modo de conta múltipla.

**As aplicações do modo dispositivo partilhado só funcionam no modo de conta única**.

> [!IMPORTANT]
> As aplicações que suportam apenas o modo de conta múltipla não podem ser executadas num dispositivo partilhado. Se um funcionário carregar uma aplicação que não suporta o modo de conta única, não será executada no dispositivo partilhado.
>
> As aplicações escritas antes do MSAL SDK ter sido lançado em modo de conta múltipla e devem ser atualizadas para suportar o modo de conta única antes de poderem ser executadas num dispositivo de modo partilhado.

**Suporte a contas simples e múltiplas**

A sua aplicação pode ser construída para suportar a execução em dispositivos pessoais e dispositivos partilhados. Se a sua aplicação suportar atualmente várias contas e pretender suportar o modo de dispositivo partilhado, adicione suporte para o modo de conta única.

Também pode querer que a sua aplicação altere o seu comportamento dependendo do tipo de dispositivo em que está a funcionar. Utilize `ISingleAccountPublicClientApplication.isSharedDevice()` para determinar quando deve ser executado no modo de conta única.

Existem duas interfaces diferentes que representam o tipo de dispositivo em que a sua aplicação se encontra. Quando solicita uma instância de aplicação da fábrica de aplicações da MSAL, o objeto de aplicação correto é fornecido automaticamente.

O modelo de objeto que se segue ilustra o tipo de objeto que pode receber e o que significa no contexto de um dispositivo partilhado:

![modelo de herança de aplicação de cliente público](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Terá de fazer uma verificação de tipo e lançar para a interface apropriada quando tiver o seu `PublicClientApplication` objeto. O código seguinte verifica o modo de conta múltipla ou o modo de conta única e lança o objeto da aplicação adequadamente:

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
| **Contas**     | Conta única | Várias contas |
| **S-in** | Global | Global |
| **Terminar sessão** | Global | Cada aplicação pode controlar se a assinatura for local para a app ou para a família de aplicações. |
| **Tipos de conta suportados** | Contas de trabalho apenas | Contas pessoais e de trabalho suportadas  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Por que pode querer apenas suportar o modo de conta única

Se estiver a escrever uma aplicação que só será utilizada para trabalhadores da primeira linha usando um dispositivo partilhado, recomendamos que escreva a sua aplicação apenas para suportar o modo de conta única. Isto inclui a maioria das aplicações focadas em tarefas, tais como aplicações de registos médicos, aplicações de faturas e a maioria das aplicações de linha de negócios. Apenas o suporte ao modo de conta única simplifica o desenvolvimento porque não precisa de implementar as funcionalidades adicionais que fazem parte de aplicações de múltiplas contas.

## <a name="what-happens-when-the-device-mode-changes"></a>O que acontece quando o modo do dispositivo muda

Se a sua aplicação estiver em funcionamento em modo de conta múltipla, e um administrador colocar o dispositivo no modo de dispositivo partilhado, todas as contas do dispositivo são retiradas da aplicação e as transições da aplicação para o modo de conta única.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Sinalização partilhada do dispositivo e o ciclo de vida geral da aplicação

Quando um utilizador se despede, terá de tomar medidas para proteger a privacidade e os dados do utilizador. Por exemplo, se estiver a construir uma aplicação de registos médicos, deverá certificar-se de que, quando o utilizador assinar registos de pacientes previamente apresentados, seja apurado. A sua candidatura deve estar preparada para isso e verificar sempre que entrar em primeiro plano.

Quando a sua aplicação utiliza o MSAL para assinar o utilizador numa aplicação em execução no dispositivo que se encontra em modo partilhado, a conta de inscrição e os tokens em cache são removidos tanto da aplicação como do dispositivo.

O diagrama seguinte mostra o ciclo de vida geral da aplicação e eventos comuns que podem ocorrer enquanto a sua aplicação é executado. O diagrama cobre a partir do momento em que uma atividade é lançada, assinando e assinando uma conta, e como eventos como pausa, retoma e paragem da atividade se encaixam.

![Ciclo de vida da aplicação do dispositivo compartilhado](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Passos seguintes

Experimente o modo Utilizar o [dispositivo partilhado no tutorial da aplicação Android](tutorial-v2-shared-device-mode.md) que mostra como executar uma aplicação de trabalhador de primeira linha num dispositivo Android em modo partilhado.
