---
title: Marque uma aplicação como editora verificada - plataforma de identidade da Microsoft / Azure
description: Descreve como marcar uma aplicação como o editor verificou. Quando uma aplicação é marcada como a editora verificada, significa que a editora verificou a sua identidade através de uma conta microsoft Partner Network que completou o processo de verificação e associou esta conta MPN ao seu registo de aplicação.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 0c1f279e49b938fb391223bec47d23326e34b2ea
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598137"
---
# <a name="mark-your-app-as-publisher-verified-preview"></a>Marque a sua aplicação como editora verificada (pré-visualização)

Quando uma aplicação é marcada como a editora verificada, significa que a editora verificou a sua identidade utilizando a sua conta Microsoft Partner Network (MPN) e associou esta conta MPN ao seu registo de aplicação. Este artigo descreve como completar o processo de [verificação da editora (pré-visualização).](publisher-verification-overview.md)

## <a name="quickstart"></a>Início Rápido
Se já está inscrito na Microsoft Partner Network (MPN) e já cumpriu os [requisitos prévios,](publisher-verification-overview.md#requirements)pode começar imediatamente: 

1. Navegue para o portal de registo de [aplicações](https://aka.ms/PublisherVerificationPreview)de pré-visualização.

1. Escolha uma aplicação e clique em **Branding**. 

1. Clique em **Adicionar ID MPN para verificar** o editor e rever os requisitos listados.

1. Introduza o seu ID MPN e clique em **Verificar e guardar**.

Para obter mais detalhes sobre benefícios, requisitos específicos e perguntas frequentes, consulte a [visão geral](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Marque a sua aplicação como editora verificada
Certifique-se de que cumpriu os [pré-requisitos](publisher-verification-overview.md#requirements)e, em seguida, siga estes passos para marcar a sua(s) app(s) como Editor Verificado.  

1. Certifique-se de que está inscrito numa conta organizacional (Azure AD) que está autorizada a fazer alterações na(s) app(s) que pretende marcar como Editor Verificado e na Conta MPN no Partner Center. 

    - No Azure AD este utilizador deve ser o Proprietário da app ou ter uma das seguintes funções: Administrador de aplicações, Administrador de Aplicações em Nuvem, Administrador Global. 

    - No Partner Center este utilizador deve ter as seguintes funções: MPN Admin, Accounts Admin ou um Global Admin (este é um papel partilhado dominado em Azure AD). 

1. Navegue para a versão de pré-visualização do portal de registo de aplicações:  

1. Clique numa aplicação que gostaria de marcar como Editor Verificado e abra a lâmina branding. 

1. Certifique-se de que o Domínio editor da aplicação está definido adequadamente. Este domínio deve ser: 

    - Ser adicionado ao inquilino da AD Azure como um domínio personalizado verificado pelo DNS,  

    - Corresponda ao domínio do endereço de e-mail utilizado durante o processo de verificação da sua conta MPN. 

1. Clique em **Adicionar ID MPN para verificar** o editor perto da parte inferior da página. 

1. Insira o seu **ID MPN**. Esta identificação mpn deve ser para: 

    - Uma conta válida da Microsoft Partner Network que concluiu o processo de verificação.  

    - A conta global partner (PGA) para a sua organização. 

1. Clique em **Verificar e guardar**. 

1. Aguarde o pedido para processar, isto pode demorar alguns minutos. 

1. Se a verificação tiver sido bem sucedida, a janela de verificação da editora fechar-se-á, devolvendo-o à lâmina de Marca. Verá um crachá azul verificado ao lado do nome de **exibição**verificado da Editora . 

1. Os utilizadores que forem solicitados a consentir com a sua aplicação começarão a ver o crachá logo após o processo ter passado com sucesso, embora possa demorar algum tempo a replicar-se em todo o sistema. 

1. Teste esta funcionalidade assinando a sua aplicação e garantindo que o crachá verificado aparece no ecrã de consentimento. Se tiver assinado como utilizador que já tenha dado o seu consentimento à aplicação, pode utilizar o parâmetro de consulta *de solicitação de* consentimento para forçar um pedido de consentimento. 

1. Repita este processo conforme necessário para quaisquer aplicações adicionais que gostaria que o crachá fosse exibido. Pode utilizar o Microsoft Graph para o fazer mais rapidamente a granel, e os cmdlets PowerShell estarão disponíveis em breve. Ver Fazer o [Microsoft API Graph pedir](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) mais informações. 

Já está! Informe-nos se tiver algum feedback sobre o processo, os resultados ou a funcionalidade em geral. 

## <a name="next-steps"></a>Passos seguintes
Se tiver problemas, leia as informações de resolução de [problemas.](troubleshoot-publisher-verification.md)