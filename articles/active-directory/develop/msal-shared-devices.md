---
title: Visão geral do modo do dispositivo partilhado
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o modo de dispositivo partilhado para permitir a partilha de dispositivos para os seus Trabalhadores da Linha Principal.
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
ms.openlocfilehash: 3cd7074467332f89d4d6c60830be34f4e2a638c1
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562088"
---
# <a name="overview-of-shared-device-mode"></a>Visão geral do modo de dispositivo partilhado

O modo dispositivo partilhado é uma funcionalidade do Azure Ative Directory que permite construir aplicações que suportem os Trabalhadores da Linha Inicial e que permitam o modo de dispositivo partilhado nos dispositivos que lhes são implantados.

>[!IMPORTANT]
> Esta característica [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-firstline-workers"></a>O que são trabalhadores da Firstline?

Os trabalhadores da Firstline são empregados de retalho, agentes de manutenção e de campo, pessoal médico e outros utilizadores que não se sentam em frente a um computador ou usam e-mails corporativos para colaboração. As secções seguintes introduzem os aspetos e desafios do suporte aos Trabalhadores da Firstline, seguidos de uma introdução às funcionalidades fornecidas pela Microsoft que permitem a sua aplicação para utilização pelos Trabalhadores da Primeira Linha de uma organização.

### <a name="challenges-of-supporting-firstline-workers"></a>Desafios do apoio aos trabalhadores da Firstline

Permitir os fluxos de trabalho do Trabalhador da Firstline inclui desafios que normalmente não são apresentados pelos trabalhadores típicos da informação. Tais desafios podem incluir uma elevada taxa de rotatividade e menos familiaridade com as principais ferramentas de produtividade de uma organização. Para capacitar os seus Trabalhadores da Primeira Linha, as organizações estão a adotar diferentes estratégias. Alguns estão a adotar uma estratégia de bring-your-your-your-device (BYOD) em que os seus colaboradores usam aplicações empresariais no seu telemóvel pessoal, enquanto outros fornecem aos seus colaboradores dispositivos partilhados, como iPads ou tablets Android.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Suporte a vários utilizadores em dispositivos concebidos para um utilizador

Uma vez que os dispositivos móveis que executam iOS ou Android foram concebidos para utilizadores individuais, a maioria das aplicações otimiza a sua experiência para uso por um único utilizador. Parte desta experiência otimizada significa permitir uma única sing-on através de aplicações e manter os utilizadores inscritos no seu dispositivo. Quando um utilizador remove a sua conta de uma aplicação, a aplicação normalmente não a considera um evento relacionado com a segurança. Muitas aplicações até mantêm as credenciais de um utilizador para um início de sção rápido. Pode até ter experimentado isto quando apagou uma aplicação do seu dispositivo móvel e depois a reinstalou, apenas para descobrir que ainda está inscrito.

### <a name="global-sign-in-and-sign-out-sso"></a>SSO global de s-in e desinsus (SSO)

Para permitir que os colaboradores de uma organização utilizem as suas apps através de um conjunto de dispositivos partilhados por esses funcionários, os desenvolvedores precisam de ativar a experiência oposta. Os colaboradores devem poder escolher um dispositivo da piscina e realizar um único gesto para "torná-lo deles" durante o seu turno. No final do seu turno, deverão ser capazes de realizar outro gesto para assinar globalmente no dispositivo, com todas as suas informações pessoais e da empresa removidas para que possam devolvê-lo à piscina do dispositivo. Além disso, se um empregado se esquecer de assinar, o dispositivo deve ser automaticamente assinado no final do seu turno e/ou após um período de inatividade.

O Azure Ative Directory permite estes cenários com uma funcionalidade chamada **modo dispositivo partilhado**.

## <a name="introducing-shared-device-mode"></a>Introdução do modo dispositivo partilhado

Como mencionado, o modo dispositivo partilhado é uma característica do Azure Ative Directory que lhe permite:

* Construir aplicações que suportem trabalhadores da Firstline
* Implementar dispositivos para os Trabalhadores da Firstline e ligar o modo de dispositivo partilhado

### <a name="build-applications-that-support-firstline-workers"></a>Construir aplicações que suportem trabalhadores da Firstline

Pode suportar os Trabalhadores da Linha Primeira nas suas aplicações utilizando a aplicação Microsoft Authentication Library (MSAL) e [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para ativar um estado de dispositivo chamado *modo de dispositivo partilhado*. Quando um dispositivo está em modo de dispositivo partilhado, a Microsoft fornece à sua aplicação informações que lhe permitam modificar o seu comportamento com base no estado do utilizador no dispositivo, protegendo os dados do utilizador.

As funcionalidades suportadas são:

* **Inscreva-se num dispositivo de utilização em toda a** parte através de qualquer aplicação suportada.
* **Inscreva um dispositivo de utilização em toda a parte** através de qualquer aplicação suportada.
* **Questione o estado do dispositivo** para determinar se a sua aplicação se encontra num dispositivo que se encontra em modo de dispositivo partilhado.
* **Consultar o estado do dispositivo do utilizador** no dispositivo para determinar se alguma coisa mudou desde a última vez que a sua aplicação foi utilizada.

O modo de suporte ao dispositivo partilhado deve ser considerado simultaneamente um melhoramento de segurança e uma atualização de funcionalidades para a sua aplicação, e pode ajudar a aumentar a sua adoção em ambientes altamente regulados, como cuidados de saúde e finanças.

Os seus utilizadores dependem de si para garantir que os seus dados não são vazados para outro utilizador. O Modo dispositivo de partilha fornece sinais úteis para indicar à sua aplicação que ocorreu uma alteração que deve gerir. A sua aplicação é responsável por verificar o estado do utilizador no dispositivo sempre que a aplicação é utilizada, limpando os dados do utilizador anterior. Isto inclui se for recarregado a partir do fundo em multi-tarefas. Numa alteração de utilizador, deve certificar-se de que ambos os dados do utilizador anterior são limpos e que quaisquer dados em cache apresentados na sua aplicação são removidos. Recomendamos que realize sempre um processo de revisão de segurança completo depois de adicionar a capacidade de modo de dispositivo partilhado à sua aplicação.

Para obter mais informações sobre como modificar as suas aplicações para suportar o modo de dispositivo partilhado, consulte a secção [etapas seguintes](#next-steps) no final deste artigo.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Implementar dispositivos para os Trabalhadores da Firstline e ligar o modo de dispositivo partilhado

Uma vez que as suas aplicações suportam o modo de dispositivo partilhado e incluem os dados e alterações de segurança necessários, pode publicitá-los como sendo utilizáveis pelos Trabalhadores da Firstline.

Os administradores de dispositivos de uma organização são capazes de implementar os seus dispositivos e aplicações nas suas lojas e locais de trabalho através de uma solução de gestão de dispositivos móveis (MDM) como o Microsoft Intune. Parte do processo de provisionamento é marcar o dispositivo como um *Dispositivo Partilhado*. Os administradores configuram o modo de dispositivo partilhado, implantando a aplicação microsoft [Authenticator](../user-help/user-help-auth-app-overview.md) e definindo o modo de dispositivo partilhado através de parâmetros de configuração. Depois de realizar estes passos, todas as aplicações que suportam o modo dispositivo partilhado utilizarão a aplicação Microsoft Authenticator para gerir o seu estado de utilizador e fornecer funcionalidades de segurança para o dispositivo e organização.

## <a name="next-steps"></a>Passos seguintes

Apoiamos plataformas iOS e Android para modo dispositivo partilhado. Reveja a documentação abaixo para que a sua plataforma comece a apoiar os Trabalhadores da Firstline nas suas aplicações.

* [Modo de dispositivo partilhado para iOS](msal-ios-shared-devices.md)
* [Modo de dispositivo partilhado para Android](msal-android-shared-devices.md)
