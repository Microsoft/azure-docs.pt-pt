---
title: Licenciamento Microsoft® Smooth Streaming Cliente Porting Kit
description: Saiba como licenciar o Kit de Porting cliente de streaming da Microsoft® Smooth Streaming.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: 59ac3ab2e70bfa00f19bae7e551780f2b2bb8ccb
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096708"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licenciamento Microsoft® Smooth Streaming Cliente Porting Kit 
## <a name="overview"></a>Descrição geral
O Microsoft Smooth Streaming Client Porting Kit **(SSPK** para abreviar) é uma implementação de cliente smooth streaming que está otimizada para ajudar fabricantes de dispositivos incorporados, operadores de cabo e mobile, fornecedores de serviços de conteúdo, fabricantes de aparelhos, fornecedores de software independentes (ISVs) e fornecedores de soluções para criar produtos e serviços para streaming de conteúdo adaptativo em formato Smooth Streaming. A SSPK é uma implementação independente de dispositivos e plataforma do cliente Smooth Streaming que pode ser portada pelo licenciado para qualquer dispositivo e plataforma. 

Incluído abaixo é uma arquitetura de alto nível e iIS Smooth Streaming Porting Kit box é a implementação smooth streaming client fornecida pela Microsoft e inclui toda a lógica central para a reprodução de conteúdos de Streaming Suave. Este conteúdo é então deportado por parceiros para um dispositivo ou plataforma específico, implementando interfaces apropriadas. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Descrição
A SSPK é licenciada em termos que oferecem excelente valor de negócio. A licença SSPK fornece à indústria:

* Fonte de kit de porting de streaming suave emC++ 
  * implementa funcionalidade do Cliente de Streaming Suave
  * adiciona análise de formato, heurística, lógica de tampão, etc.
* APIs de aplicação do jogador 
  * interfaces de programação para interação com uma aplicação de media player
* Interface de camada de abstração da plataforma (PAL) 
  * interfaces de programação para interação com o sistema operativo (fios, tomadas)
* Interface da camada de abstração de hardware (HAL) 
  * interfaces de programação para interação com descodificadores de hardware A/V (descodificação, renderização)
* Interface de Gestão de Direitos Digitais (DRM) 
  * interfaces de programação para manuseamento de DrM através da Camada de Abstração DRM (DAL)
  * O Microsoft PlayReady Porting Kit envia-se separadamente, mas integra-se através desta interface. Para mais detalhes sobre o licenciamento do Dispositivo Microsoft PlayReady, clique [aqui](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Amostras de implementação 
  * implementação pal da amostra para linux
  * implementação de HAL da amostra para gStreamer

## <a name="licensing-options"></a>Opções de Licenciamento
O Microsoft Smooth Streaming Client Porting Kit é disponibilizado aos licenciados ao abrigo de dois acordos de licença distintos: um para desenvolver produtos provisórios de cliente de streaming liso e outro para distribuir Produtos Finais de Cliente de Streaming Liso para utilizadores finais.

* Para fabricantes de chipset, integradores de sistemas ou fornecedores de software independentes (ISVs) que necessitem de um kit de porting de código fonte para desenvolver Produtos Provisórios, deve ser executado um Microsoft Smooth Streaming Client Porting Kit **Interim Product License.**
* Para fabricantes de dispositivos ou ISVs que necessitem de direitos de distribuição para produtos finais de cliente de streaming liso para utilizadores finais, a **Licença final** de produto do Microsoft Smooth Streaming Client Porting Kit final deve ser executada.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Cliente Porting Kit Licença provisória de produto
Ao abrigo desta licença, a Microsoft oferece um Kit de Porting De Cliente de Streaming E os direitos de propriedade intelectual necessários para desenvolver e distribuir Produtos Provisórios de Cliente de Streaming Liso para outros licenciados de dispositivos porting kit de cliente de streaming suave que distribuir Produtos Finais do Cliente de Streaming Suave.

#### <a name="fee-structure"></a>Estrutura de taxas
Uma taxa de licença única de $50.000 fornece acesso ao Kit de Porting de Clientes de Streaming Suave. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming Cliente Porting Kit Licença final de produto
Ao abrigo desta licença, a Microsoft oferece todos os direitos de propriedade intelectual necessários para receber produtos provisórios de cliente de streaming liso de outros licenciados de Kit de Porting De cliente de streaming liso e para distribuir a marca de empresa Smooth Streaming Client Client Final Produtos para utilizadores finais.

#### <a name="fee-structure"></a>Estrutura de taxas
O Produto Final do Cliente de Streaming Suave é oferecido sob um modelo de royalties como em:

* $0,10 por dispositivo de implementação enviado
* A realeza está limitada a $50.000 por ano.
* Não há realeza para as primeiras 10.000 implementações de dispositivos por ano 

## <a name="licensing-procedure-and-sspk-access"></a>Procedimento de Licenciamento e acesso sSPK
E-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) para todas as consultas de licenciamento.

O [portal de distribuição SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) está acessível a licenciados provisórios registados.

Os licenciados provisórios e finais da SSPK podem submeter questões técnicas a [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming Cliente Provisório Contrato de Produto Licenciado

* Adroit Business Solutions, Inc
* Transmissão Digital Avançada SA
* AirTies Kablosuz Iletismo Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Corporação Alticast
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar Purchaseing Corporation
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Liberty Global Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffron Digital Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Corporação Sony
* Tecnologia Tatung Inc.
* Top Victory Investments, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* Corporação ZTE

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming Cliente Final Product Agreement Licensees
* Transmissão Digital Avançada SA
* AirTies Kablosuz Iletismo Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* British Sky Broadcasting Limited
* CastPal Technology Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar Purchaseing Corporation
* Enseo, Inc.
* Filmes FilmFlex Limited
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson Innovations Limited
* Haier Information Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* Corporação KDDI
* Nintendo Co., Ltd.
* Laranja SA
* Saffron Digital Limited
* Sagemcom Banda Larga SAS
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Corporação Sony
* Tecnologias de Entrega Technicolor, SAS
* Tongfang Global Ltd.
* Top Victory Investments, Ltd.
* Toshiba Lifestyle Products and Services Corporation
* Universal Media Corporation /Eslováquia/ s.r.o.
* VIZIO, Inc.
* Corporação Wistron
* Corporação ZTE

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

