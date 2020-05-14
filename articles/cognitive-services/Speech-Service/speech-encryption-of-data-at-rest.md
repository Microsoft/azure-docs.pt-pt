---
title: Encriptação do serviço de fala de dados em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação do serviço de fala de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: c2e52fbab8d984f7442d8a336e90e9f22c0bf061
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198658"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Encriptação do serviço de fala de dados em repouso

O Serviço de Fala encripta automaticamente os seus dados quando é persistido na nuvem. A encriptação do serviço de fala protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos

Os dados são encriptados e desencriptados utilizando encriptação [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) compatível com [140-2](https://en.wikipedia.org/wiki/FIPS_140-2) bits. A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os seus dados são seguros por padrão e não precisa de modificar o seu código ou aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Quando utilizar o Discurso Personalizado e a Voz Personalizada, o serviço de fala pode armazenar os seguintes dados na nuvem:  

* Dados de rastreio da fala - apenas se ligar o traço para o seu ponto final personalizado
* Dados de treino e teste carregados

Por padrão, os seus dados são armazenados no armazenamento da Microsoft e a sua subscrição utiliza chaves de encriptação geridas pela Microsoft. Também tem a opção de preparar a sua própria conta de armazenamento. O acesso à loja é gerido pela Identidade Gerida, e o serviço de Fala não pode aceder diretamente aos seus próprios dados, tais como dados de rastreio de fala, dados de formação de personalização e modelos personalizados.

Para obter mais informações sobre identidade gerida, consulte [as identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Traga o seu próprio armazenamento (BYOS) para personalização e exploração madeireira

Para solicitar acesso para trazer o seu próprio armazenamento, preencha e envie o serviço de Fala - traga o seu próprio formulário de pedido de [armazenamento (BYOS).](https://aka.ms/cogsvc-cmk) Uma vez aprovado, terá de criar a sua própria conta de armazenamento para armazenar os dados necessários para a personalização e registo. Ao adicionar uma conta de armazenamento, o recurso do serviço Speech permitirá um sistema de identidade gerida. Após a ativação da identidade gerida atribuída, este recurso será registado no Azure Ative Directory (AAD). Após a sua registo, a identidade gerida terá acesso à conta de armazenamento. Pode saber mais sobre identidades geridas aqui. Para obter mais informações sobre identidade gerida, consulte [as identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Se desativar as identidades geridas atribuídas, o acesso à conta de armazenamento será removido. Isto fará com que as partes do serviço da Fala que exigem o acesso à conta de armazenamento deixem de funcionar.  

O serviço de Fala não suporta atualmente o Bloqueio do Cliente. No entanto, os dados do cliente podem ser armazenados utilizando BYOS, permitindo-lhe obter controlos de dados semelhantes ao Bloqueio do [Cliente](../../security/fundamentals/customer-lockbox-overview.md). Tenha em mente que os dados do serviço da Fala permanecem e são processados na região onde o recurso Speech foi criado. Isto aplica-se a quaisquer dados em repouso e dados em trânsito. Ao utilizar funcionalidades de personalização, como Discurso Personalizado e Voz Personalizada, todos os dados do cliente são transferidos, armazenados e processados na mesma região onde residem os seus recursos de serviço BYOS (se utilizados) e de serviço de Fala.

> [!IMPORTANT]
> A Microsoft **não** utiliza dados dos clientes para melhorar os seus modelos De Discurso. Além disso, se o registo de pontos finais for desativado e não forem utilizadas personalizações, então não são armazenados dados do cliente. 

## <a name="next-steps"></a>Próximos passos

* [Serviço de fala - traga o seu próprio formulário de pedido de armazenamento (BYOS)](https://aka.ms/cogsvc-cmk)
* [O que são identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
