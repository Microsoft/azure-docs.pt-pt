---
title: Resolução de problemas Azure Data Factory UX
description: Saiba como resolver problemas com os problemas da Azure Data Factory UX.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87568008"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Problemas de resolução de dados Azure Data Factory UX Problemas
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para a Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>ADF UX não está a carregar

> [!NOTE]
> O Azure Data Factory UX suporta oficialmente o Microsoft Edge e o Google Chrome. A utilização de outros navegadores web pode levar a comportamentos inesperados ou indocumentais.

### <a name="third-party-cookies-blocked"></a>Cookies de terceiros bloqueados

O ADF UX utiliza cookies de navegador para persistir na sessão do utilizador e permitir experiências de desenvolvimento e monitorização interativas. É possível que o seu navegador bloqueie cookies de terceiros porque está a usar uma sessão incógnita ou tem um bloqueador de anúncios ativado. Bloquear cookies de terceiros pode causar problemas ao carregar o portal, como ser redirecionado para uma página em branco, https://adf.azure.com/accesstoken.html ou receber uma mensagem de aviso dizendo que os cookies de terceiros estão bloqueados. Para resolver este problema, ative as opções de cookies de terceiros no seu navegador utilizando os seguintes passos:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Permitir todos os cookies

1. Visite **chrome://settings/cookies** no seu browser.
1. Selecione **Permitir todas as opções** de cookies ![ Chrome-Allow-All-Cookies](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Refresque a ADF UX e tente novamente.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Apenas permitir que a ADF UX utilize cookies
Se não quiser permitir todos os cookies, pode opcionalmente apenas permitir ADF UX:
1. Visite **chrome://settings/cookies.**
1. Clique em **adicionar** em **Sites que podem sempre usar cookies** ![ opção Adicionar ADF UX para sites permitidos](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Adicione **adf.azure.com** site, verifique **todas as opções de cookies** e guarde. ![Permitir todos os cookies do site da ADF UX](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Refresque a ADF UX e tente novamente.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Visite **edge://settings/content/cookies** no seu browser.
1. Certifique-se **de que os sites para guardar e ler dados de cookies** estão ativados e que a opção de **cookies de terceiros do Bloco é desativada** ![ Permitir todos os cookies no Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Refresque a ADF UX e tente novamente.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Apenas permitir que a ADF UX utilize cookies

Se não quiser permitir todos os cookies, pode opcionalmente apenas permitir ADF UX:

1. Visite **edge://settings/content/cookies.**
1. Na secção **Permitir,** clique em **Adicionar** e adicione **adf.azure.com** site. ![Adicione ADF UX a sites permitidos](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Refresque a ADF UX e tente novamente.

## <a name="next-steps"></a>Próximos passos

Para obter mais ajuda para resolver problemas, experimente estes recursos:

* [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vídeos Azure](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
