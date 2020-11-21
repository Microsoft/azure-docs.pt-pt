---
title: Encriptação do serviço de fala dos dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de encriptação geridas pela Microsoft e também permite gerir as suas subscrições de Serviços Cognitivos com as suas próprias chaves, chamadas teclas geridas pelo cliente (CMK). Este artigo cobre a encriptação de dados em repouso para o serviço Speech.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: aa0fe33dff0161767b74546aad49003d8fc70c16
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015261"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Encriptação do serviço de fala dos dados em repouso

O Serviço de Fala encripta automaticamente os seus dados quando estes são persistidos na nuvem. A encriptação do serviço de fala protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos

Os dados são encriptados e desencriptados utilizando encriptação AES de [140-2](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) em conformidade com o [FIPS.](https://en.wikipedia.org/wiki/FIPS_140-2) A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os dados estão protegidos por predefinição e não precisa de modificar o código ou as aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Quando utilizar a Fala Personalizada e a Voz Personalizada, o serviço de fala pode armazenar os seguintes dados na nuvem:  

* Dados de rastreio de fala - apenas se a sua vez o rastreio ligado para o seu ponto final personalizado
* Dados de treino e teste carregados

Por predefinição, os seus dados são armazenados no armazenamento da Microsoft e a sua subscrição utiliza chaves de encriptação geridas pela Microsoft. Também tem a opção de preparar a sua própria conta de armazenamento. O acesso à loja é gerido pela Identidade Gerida, e o serviço de Fala não pode aceder diretamente aos seus próprios dados, tais como dados de traços de fala, dados de formação de personalização e modelos personalizados.

Para obter mais informações sobre identidade gerida, consulte [o que são identidades geridas.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Traga o seu próprio armazenamento (BYOS) para personalização e registo

Para solicitar o acesso para trazer o seu próprio armazenamento, preencha e submeta o [serviço Dea palavra - traga o seu próprio formulário de pedido de armazenamento (BYOS).](https://aka.ms/cogsvc-cmk) Uma vez aprovado, terá de criar a sua própria conta de armazenamento para armazenar os dados necessários para personalização e registo. Ao adicionar uma conta de armazenamento, o recurso de serviço Speech permitirá uma identidade gerida atribuída ao sistema. Após o sistema atribuído à identidade gerida, este recurso será registado no Azure Ative Directory (AAD). Após o registo, a identidade gerida terá acesso à conta de armazenamento. Pode saber mais sobre identidades geridas aqui. Para obter mais informações sobre identidade gerida, consulte [o que são identidades geridas.](../../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Se desativar as identidades geridas do sistema, o acesso à conta de armazenamento será removido. Isto fará com que as partes do serviço Discurso que exigem o acesso à conta de armazenamento deixem de funcionar.  

O serviço de discurso não suporta atualmente o Lockbox do Cliente. No entanto, os dados do cliente podem ser armazenados utilizando BYOS, permitindo-lhe obter controlos de dados semelhantes ao [Lockbox do Cliente.](../../security/fundamentals/customer-lockbox-overview.md) Tenha em mente que os dados do serviço de fala permanecem e são processados na região onde o recurso Discurso foi criado. Isto aplica-se a quaisquer dados em repouso e dados em trânsito. Ao utilizar funcionalidades de personalização, como Discurso Personalizado e Voz Personalizada, todos os dados do cliente são transferidos, armazenados e processados na mesma região onde residem os seus recursos DE SERVIÇO BYOS (se utilizados) e serviço de fala.

> [!IMPORTANT]
> A Microsoft **não** utiliza os dados dos clientes para melhorar os seus modelos De Discurso. Além disso, se a sessão de registo de pontos finais for desativada e não forem utilizadas personalizações, não são armazenados dados do cliente. 

## <a name="next-steps"></a>Próximos passos

* [Serviço de fala - traga o seu próprio formulário de pedido de armazenamento (BYOS)](https://aka.ms/cogsvc-cmk)
* [O que são identidades geridas.](../../active-directory/managed-identities-azure-resources/overview.md)