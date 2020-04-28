---
title: Visão geral do modo do dispositivo compartilhado
titleSuffix: Microsoft identity platform | Azure
description: Aprenda sobre o modo de dispositivo partilhado para permitir a partilha de dispositivos para os seus Trabalhadores da Linha Firstline.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550243"
---
# <a name="overview-of-shared-device-mode"></a>Visão geral do modo de dispositivo partilhado

O modo de dispositivo partilhado é uma funcionalidade do Diretório Ativo Azure que permite construir aplicações que suportem os Trabalhadores da Linha First e ativar o modo de dispositivo partilhado nos dispositivos que os dispositivos são implantados.

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="what-are-firstline-workers"></a>O que são trabalhadores da Firstline?

Os Trabalhadores da Firstline são empregados de retalho, agentes de manutenção e campo, pessoal médico e outros utilizadores que não se sentam em frente a um computador ou usam e-mails corporativos para colaboração. As seguintes secções introduzem os aspetos e desafios de apoiar a Firstline Workers, seguidas de uma introdução às funcionalidades fornecidas pela Microsoft que permitem a sua aplicação para utilização pelos Trabalhadores firstline de uma organização.

### <a name="challenges-of-supporting-firstline-workers"></a>Desafios de apoiar os trabalhadores da Firstline

Permitir os fluxos de trabalho do Trabalhador em Primeira Linha inclui desafios que normalmente não são apresentados pelos trabalhadores da informação típica. Estes desafios podem incluir uma elevada taxa de rotatividade e uma menor familiaridade com as ferramentas de produtividade fundamentais de uma organização. Para capacitar os seus Trabalhadores de Primeira Linha, as organizações estão a adotar diferentes estratégias. Alguns estão a adotar uma estratégia de trazer o seu próprio dispositivo (BYOD) na qual os seus colaboradores usam aplicações empresariais no seu telemóvel pessoal, enquanto outros fornecem aos seus colaboradores dispositivos partilhados, como iPads ou tablets Android.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Apoiar vários utilizadores em dispositivos concebidos para um utilizador

Uma vez que os dispositivos móveis que executam iOS ou Android foram concebidos para utilizadores individuais, a maioria das aplicações otimiza a sua experiência para uso por um único utilizador. Parte desta experiência otimizada significa permitir um único início de sessão entre aplicações e manter os utilizadores inscritos no seu dispositivo. Quando um utilizador remove a sua conta de uma aplicação, a aplicação normalmente não a considera um evento relacionado com a segurança. Muitas aplicações até mantêm as credenciais de um utilizador por perto para um início rápido. Pode até ter experimentado isto quando apagou uma aplicação do seu dispositivo móvel e depois o reinstalou, apenas para descobrir que ainda está inscrito.

### <a name="global-sign-in-and-sign-out-sso"></a>Inscrição e inscrição global (SSO)

Para permitir que os colaboradores de uma organização utilizem as suas apps através de um conjunto de dispositivos partilhados por esses colaboradores, os desenvolvedores precisam de permitir a experiência oposta. Os colaboradores devem poder escolher um dispositivo da piscina e realizar um único gesto para "torná-lo deles" durante o seu turno. No final do seu turno, devem poder realizar outro gesto para assinar globalmente no dispositivo, com todas as informações pessoais e da empresa removidas para que possam devolvê-lo à piscina do dispositivo. Além disso, se um trabalhador se esquecer de assinar, o dispositivo deve ser automaticamente assinado no final do seu turno e/ou após um período de inatividade.

O Azure Ative Directory permite estes cenários com uma funcionalidade chamada **modo de dispositivo partilhado**.

## <a name="introducing-shared-device-mode"></a>Introdução do modo de dispositivo partilhado

Como mencionado, o modo de dispositivo partilhado é uma funcionalidade do Diretório Ativo Azure que lhe permite:

* Construir aplicações que apoiem os trabalhadores da Firstline
* Desloque os dispositivos para os Trabalhadores da Linha First e ligue o modo de dispositivo partilhado

### <a name="build-applications-that-support-firstline-workers"></a>Construir aplicações que apoiem os trabalhadores da Firstline

Pode suportar os Trabalhadores da Linha Firstline nas suas aplicações utilizando a Microsoft Authentication Library (MSAL) e a [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para ativar um estado de dispositivo chamado *modo de dispositivo partilhado*. Quando um dispositivo está no modo de dispositivo partilhado, a Microsoft fornece à sua aplicação informações que lhe permitem modificar o seu comportamento com base no estado do utilizador no dispositivo, protegendo os dados dos utilizadores.

As funcionalidades suportadas são:

* **Inscreva-se num dispositivo de utilizador** através de qualquer aplicação suportada.
* **Assine um dispositivo de utilizador através** de qualquer aplicação suportada.
* **Consultar o estado do dispositivo** para determinar se a sua aplicação está num dispositivo que se encontra em modo de dispositivo partilhado.
* **Consultar o estado do dispositivo do utilizador** no dispositivo para determinar se algo mudou desde a última vez que a sua aplicação foi utilizada.

O modo de suporte do dispositivo partilhado deve ser considerado um melhoramento de segurança e um upgrade de funcionalidades para a sua aplicação, e pode ajudar a aumentar a sua adoção em ambientes altamente regulados, como cuidados de saúde e finanças.

Os seus utilizadores dependem de si para garantir que os seus dados não sejam vazados para outro utilizador. O Modo de Partilha do Dispositivo fornece sinais úteis para indicar à sua aplicação que ocorreu uma alteração que deve gerir. A sua aplicação é responsável por verificar o estado do utilizador no dispositivo sempre que a aplicação é utilizada, limpando os dados do utilizador anterior. Isto inclui se for recarregado a partir do fundo em multi-tarefas. Numa alteração de utilizador, deve certificar-se de que os dados do utilizador anterior são limpos e que os dados em cache apresentados na sua aplicação são removidos. Recomendamos que realize sempre um processo de revisão de segurança completo depois de adicionar a capacidade do modo de dispositivo partilhado à sua aplicação.

Para obter mais informações sobre como modificar as suas aplicações para suportar o modo de dispositivo partilhado, consulte a secção [Depassos Seguintes](#next-steps) no final deste artigo.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Desloque os dispositivos para os Trabalhadores da Linha First e ligue o modo de dispositivo partilhado

Assim que as suas aplicações suportam o modo de dispositivo partilhado e incluam as alterações de dados e segurança necessárias, pode publicitá-las como sendo utilizáveis pela Firstline Workers.

Os administradores de dispositivos de uma organização são capazes de implementar os seus dispositivos e aplicações para as suas lojas e locais de trabalho através de uma solução de gestão de dispositivos móveis (MDM) como a Microsoft Intune. Parte do processo de provisionamento está a marcar o dispositivo como *dispositivo partilhado*. Os administradores configuram o modo de dispositivo partilhado implementando a [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e definindo o modo de dispositivo partilhado através de parâmetros de configuração. Após a realização destas etapas, todas as aplicações que suportam o modo de dispositivo partilhado utilizarão a aplicação Microsoft Authenticator para gerir o seu estado de utilizador e fornecer funcionalidades de segurança para o dispositivo e organização.

## <a name="next-steps"></a>Passos seguintes

Apoiamos plataformas iOS e Android para o modo de dispositivo partilhado. Reveja a documentação abaixo para que a sua plataforma comece a apoiar a Firstline Workers nas suas aplicações.

* [Suporte ao modo de dispositivo partilhado para iOS](msal-ios-shared-devices.md)
* [Suporte ao modo de dispositivo partilhado para Android](msal-android-shared-devices.md)
