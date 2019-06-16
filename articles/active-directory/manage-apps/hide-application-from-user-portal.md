---
title: Ocultar uma aplicação da experiência do utilizador no Azure Active Directory | Documentos da Microsoft
description: Como ocultar uma aplicação da experiência do usuário em painéis de acesso do Azure Active Directory ou Office 365 iniciadores.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd98aa974f2adcd363c04c10b7a10cef6ca8ce7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824544"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Ocultar aplicações dos utilizadores finais no Azure Active Directory

Instruções sobre como ocultar aplicações no painel de My Apps ou no iniciador de Office 365 dos utilizadores finais. Quando um aplicativo está oculta, os usuários ainda têm permissões para a aplicação. 

## <a name="prerequisites"></a>Pré-requisitos

São necessários privilégios de administrador do aplicativo para ocultar uma aplicação a partir do painel de My Apps e o Iniciador do Office 365.

São necessários privilégios de administrador global para ocultar todos os aplicativos do Office 365.


## <a name="hide-an-application-from-the-end-user"></a>Ocultar uma aplicação do utilizador final
Utilize os seguintes passos para ocultar uma aplicação a partir do painel de My Apps e iniciador de aplicações do Office 365.

1.  Inicie sessão para o [portal do Azure](https://portal.azure.com) como administrador global para o seu diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **aplicações empresariais**. O **aplicações empresariais - todas as aplicações** é aberto o painel.
4.  Sob **tipo de aplicação**, selecione **aplicações empresariais**, se ainda não estiver selecionada.
5.  Procure a aplicação que pretende ocultar e clique na aplicação.  Descrição geral da aplicação é aberta.
6.  Clique em **Propriedades**. 
7.  Para o **visível aos utilizadores?** pergunta, clique em **não**.
8.  Clique em **Guardar**.


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Ocultar aplicações do Office 365, do painel de My Apps

Utilize os seguintes passos para ocultar todos os aplicativos do Office 365 do painel de MyApps. Os aplicativos são ainda visíveis no portal do Office 365.

1.  Inicie sessão para o [portal do Azure](https://portal.azure.com) como um administrador global do seu diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **definições de utilizador**.
4.  Sob **aplicações empresariais**, clique em **gerir como os utilizadores finais iniciam e veem seus aplicativos.**
5.  Para **os utilizadores apenas podem ver aplicações do Office 365 no portal do Office 365**, clique em **Sim**.
6.  Clique em **Guardar**.


## <a name="next-steps"></a>Passos Seguintes
* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial](remove-user-or-group-access-portal.md)
* [Alterar o nome ou logótipo de uma aplicação empresarial](change-name-or-logo-portal.md)

