---
title: Licenciamento do Microsoft® Smooth Streaming Client Porting kit
description: Saiba mais sobre como licenciar o kit de portabilidade do cliente do Microsoft® Smooth Streaming.
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
ms.openlocfilehash: 3e7742e7035f3c4f4827a2edae1ad5a705aa221f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693420"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licenciamento do Microsoft® Smooth Streaming Client Porting kit 
## <a name="overview"></a>Visão geral
O Microsoft Smooth Streaming Client Porting Kit (**SSPK** para curto) é uma implementação de cliente Smooth streaming que é otimizada para ajudar os fabricantes de dispositivos incorporados, cabos e operadores móveis, provedores de serviços de conteúdo, fabricantes de telefones, ISVs (fornecedores independentes de software) e provedores de soluções para criar produtos e serviços para streaming de conteúdo adaptável no formato Smooth Streaming. SSPK é uma implementação de dispositivo e de plataforma independente de Smooth Streaming cliente que pode ser transportada pelo licenciado para qualquer dispositivo e plataforma. 

A seguir está uma arquitetura de alto nível e a caixa do kit de portabilidade do IIS Smooth Streaming é a implementação do cliente Smooth Streaming fornecida pela Microsoft e inclui toda a lógica principal para reprodução de conteúdo de Smooth Streaming. Esse conteúdo é então portado por parceiros para um dispositivo ou plataforma específica implementando interfaces apropriadas. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Descrição
O SSPK é licenciado em termos que oferecem excelente valor comercial. A licença do SSPK fornece ao setor:

* Origem do kit de portabilidade de Smooth Streaming noC++ 
  * implementa Smooth Streaming funcionalidade de cliente
  * Adiciona análise de formato, heurística, lógica de buffer, etc.
* APIs de aplicativo de Player 
  * interfaces de programação para interação com um aplicativo de player de mídia
* Interface de camada de abstração de plataforma (PAL) 
  * interfaces de programação para interação com o sistema operacional (threads, soquetes)
* Interface HAL (hardware Abstraction Layer) 
  * interfaces de programação para interação com decodificadores de hardware A/V (decodificação, renderização)
* Interface de Rights Management digital (DRM) 
  * interfaces de programação para tratamento de DRM por meio da DAL (camada de abstração de DRM)
  * O Microsoft PlayReady Porting kit é fornecido separadamente, mas integra-se por meio dessa interface. Para obter mais detalhes sobre o licenciamento de dispositivo do Microsoft PlayReady, clique [aqui](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Exemplos de implementação 
  * exemplo de implementação de PAL para Linux
  * implementação de exemplo de HAL para GStreamer

## <a name="licensing-options"></a>Opções de Licenciamento
O Microsoft Smooth Streaming Client Porting kit é disponibilizado para licenciados sob dois contratos de licença distintos: um para desenvolver Smooth Streaming produtos provisórios do cliente e outro para distribuir Smooth Streaming produtos finais do cliente aos usuários finais.

* Para fabricantes de chipsets, integradores de sistemas ou ISVs (fornecedores independentes de software) que exigem um kit de portabilidade de código-fonte para desenvolver produtos provisórios, uma **licença de produto provisória** do Microsoft Smooth streaming client Porting kit deve ser executada.
* Para fabricantes de dispositivos ou ISVs que exigem direitos de distribuição para Smooth Streaming produtos finais de cliente aos usuários finais, a **licença de produto final** do Microsoft Smooth streaming client portation kit deve ser executada.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Licença de produto provisório do Microsoft Smooth Streaming Client Porting kit
Sob essa licença, a Microsoft oferece um kit de portabilidade de cliente Smooth Streaming e os direitos de propriedade intelectual necessários para desenvolver e distribuir Smooth Streaming produtos provisórios do cliente a outros Smooth Streaming licenciados do dispositivo do kit de portabilidade do cliente que Distribua Smooth Streaming produtos finais do cliente.

#### <a name="fee-structure"></a>Estrutura de taxas
Uma taxa de licença de uso único dos EUA $50000 fornece acesso ao kit de portabilidade de cliente Smooth Streaming. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Licença de produto final do Microsoft Smooth Streaming Client Porting kit
Sob essa licença, a Microsoft oferece todos os direitos de propriedade intelectual necessários para receber Smooth Streaming produtos provisórios de cliente de outros licenciados do kit de porta de cliente Smooth Streaming e para distribuir a marca da empresa Smooth Streaming cliente final Produtos para usuários finais.

#### <a name="fee-structure"></a>Estrutura de taxas
O produto cliente Smooth Streaming final é oferecido sob um modelo de royalty como em:

* $0.10 a implementação por dispositivo foi enviada
* O Royalty está limitado às $50000 por ano
* Nenhum Royalty para as primeiras implementações de dispositivo 10.000 a cada ano 

## <a name="licensing-procedure-and-sspk-access"></a>Procedimento de licenciamento e acesso SSPK
[@No__t_1](mailto:sspkinfo@microsoft.com) de email para todas as consultas de licenciamento.

O [portal de distribuição SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) pode ser acessado para licenciados provisórios registrados.

As licenças de SSPK provisórias e finais podem enviar perguntas técnicas para [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Licenciados do contrato de produto provisório do Microsoft Smooth Streaming Client

* Adroit Business Solutions, Inc
* SA de difusão digital avançada
* Saties Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltda.
* Alticast Corporation
* Amazon digital Services, Inc.
* Arion Technology, Inc.
* Software de multimídia AVC co., Ltd.
* Cavium, Inc.
* EchoStar de compra corporativa
* Enseo, Inc.
* Fluendo S.A.
* HANDAN BroadInfoCom co., Ltd.
* Infomir GMBH
* Irdeto EUA Inc.
* iWEDIA S.A. 
* Serviços globais do Liberty BV
* MediaTek Inc.
* MStar co, Ltd
* Nintendo co., Ltd.
* OpenTV, Inc.
* Saffron Digital Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* Co., Ltd. da Huizhou (eletrônica de tecnologia TCL)
* Principais investimentos em vitória, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* Visualização, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Licenciados do contrato de produto final do Microsoft Smooth Streaming Client
* SA de difusão digital avançada
* Saties Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltda.
* Amazon digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONIK SAN. VE JOGO. A. Ş
* Transmissão limitada do céu britânico
* CastPal Technology Inc., Shenzhen
* O Compal Electronics, Inc.
* Dongguan digital AV Technology Corp., Ltd.
* EchoStar de compra corporativa
* Enseo, Inc.
* Filmes FilmFlex limitados
* Fluendo S.A.
* FUNAI ELÉTRICO CO., LTD
* Inovações Gibson limitadas
* Haier de informação S. R. L
* HANDAN BroadInfoCom co., Ltd.
* Hisense International Co., Ltd. 
* Homecast co., Ltd
* Está Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia co., Ltd.
* KDDI Corporation
* Nintendo co., Ltd.
* Orange SA
* Saffron Digital Limited
* SAS de banda larga Sagemcom
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* CO., LTD. da Shenzhen de eletrônicos de envio
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin industrial Corp.
* Céu Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome
* Sony Corporation
* Limitada por marketing no exterior (RAE de Macau comercial)
* Tecnologias de entrega Technicolor, SAS
* Tongfang global Ltd.
* Principais investimentos em vitória, Ltd.
* Produtos de estilo de vida da Toshiba & Services Corporation
* Universal Media Corporation/Slovakia/s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

