---
title: Marque uma app como editor verificado - Plataforma de identidade da Microsoft Rio Azure
description: Descreve como marcar uma aplicação como editor verificado. Quando uma aplicação é marcada como editora verificada, significa que a editora verificou a sua identidade utilizando uma conta da Microsoft Partner Network que concluiu o processo de verificação e associou esta conta MPN ao seu registo de candidatura.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: b3c3bed20b5fd60b9323dada617fb1302efc41d2
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006949"
---
# <a name="mark-your-app-as-publisher-verified"></a>Marcar a sua aplicação como verificada pelo publicador

Quando uma aplicação é marcada como editora verificada, significa que o editor verificou a sua identidade utilizando a sua conta Microsoft Partner Network (MPN) e associou esta conta MPN ao seu registo de candidatura. Este artigo descreve como completar o processo de verificação do [editor (pré-visualização).](publisher-verification-overview.md)

## <a name="quickstart"></a>Início Rápido
Se já está inscrito na Microsoft Partner Network (MPN) e cumpriu os [requisitos prévios,](publisher-verification-overview.md#requirements)pode começar imediatamente: 

1. Navegue para o [portal de Registo de Aplicações.](https://aka.ms/PublisherVerificationPreview)

1. Escolha uma aplicação e clique em **Branding.** 

1. Clique **em Adicionar MPN ID para verificar o editor** e rever os requisitos listados.

1. Introduza o seu ID MPN e clique **em Verificar e guardar**.

Para obter mais detalhes sobre benefícios específicos, requisitos e perguntas frequentes consulte a [visão geral.](publisher-verification-overview.md)


## <a name="mark-your-app-as-publisher-verified"></a>Marcar a sua aplicação como verificada pelo publicador
Certifique-se de que cumpriu os [requisitos prévios,](publisher-verification-overview.md#requirements)em seguida, siga estes passos para marcar as suas aplicações como Editor Verificado.  

1. Certifique-se de que está assinado com uma conta organizacional (Azure AD) que está autorizada a fazer alterações nas aplicações que pretende marcar como Editora Verificada e na Conta MPN no Partner Center. 

    - No Azure AD este utilizador deve ser o Proprietário da app ou ter uma das seguintes funções: Administrador de Aplicação, Cloud Application Admin, Global Admin. 

    - No Partner Center este utilizador deve ter as seguintes funções: MPN Admin, Accounts Admin ou um Administrador Global (este é um papel partilhado dominado no Azure AD). 

1. Navegue para o portal de Registo de Aplicações:  

1. Clique numa aplicação que gostaria de marcar como Editora Verificada e abra a lâmina de marcação. 

1. Certifique-se de que o Domínio de Editores da aplicação está definido de forma adequada. Este domínio deve ser: 

    - Ser adicionado ao inquilino Azure AD como um domínio personalizado verificado pelo DNS,  

    - Corresponda ao domínio do endereço de e-mail utilizado durante o processo de verificação da sua conta MPN. 

1. Clique **em Adicionar ID MPN para verificar o editor** perto da parte inferior da página. 

1. Insira o seu **ID MPN**. Este ID MPN deve ser para: 

    - Uma conta válida da Microsoft Partner Network que completou o processo de verificação.  

    - A conta global partner (PGA) para a sua organização. 

1. Clique **em Verificar e guardar**. 

1. Aguarde o processo, isto pode demorar alguns minutos. 

1. Se a verificação tiver sido bem sucedida, a janela de verificação do editor fechar-se-á, devolvendo-o à lâmina de marcação. Você verá um crachá azul verificado ao lado do seu nome de **exibição da Editora**verificada . 

1. Os utilizadores que forem solicitados a consentirem com a sua app começarão a ver o crachá logo após o processo ter passado pelo processo com sucesso, embora possa demorar algum tempo para que este se reproduza em todo o sistema. 

1. Teste esta funcionalidade ao iniciar sessão na sua aplicação e certifique-se de que o crachá verificado aparece no ecrã de consentimento. Se você estiver assinado como um utilizador que já concedeu o consentimento à app, pode usar o parâmetro de consulta *de pedido de consentimento de prompt=para* forçar uma solicitação de consentimento. 

1. Repita este processo conforme necessário para quaisquer aplicações adicionais para as quais pretende que o crachá seja exibido. Pode utilizar o Microsoft Graph para o fazer mais rapidamente a granel, e os cmdlets PowerShell estarão disponíveis em breve. Ver [Fazer gráfico aPI do Microsoft pede](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) mais informações. 

Já está! Informe-nos se tiver algum feedback sobre o processo, os resultados ou a funcionalidade em geral. 

## <a name="next-steps"></a>Próximos passos
Se tiver problemas, leia a [informação relativa à resolução de problemas.](troubleshoot-publisher-verification.md)
