---
title: Licenciamento Microsoft &reg; Smooth Streaming Cliente Kit de Porting
description: Saiba como licenciar o Kit de Porting do &reg; Cliente de Streaming Do Microsoft.
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
ms.openlocfilehash: 8e2217c04f5c39ad93185273d537324f4b56e456
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099545"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licenciamento Microsoft &reg; Smooth Streaming Cliente Kit de Porting

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
## <a name="overview"></a>Descrição geral
O Microsoft Smooth Streaming Client Porting Kit **(SSPK** para abreviar) é uma implementação do cliente de Streaming Suave que é otimizada para ajudar fabricantes de dispositivos incorporados, operadores de cabos e móveis, fornecedores de serviços de conteúdo, fabricantes de aparelhos, fornecedores de software independentes (ISVs) e fornecedores de soluções para criar produtos e serviços para streaming de conteúdos adaptativos em formato Smooth Streaming. A SSPK é um dispositivo e implementação independente da plataforma do cliente Smooth Streaming que pode ser transmitida pelo licenciado a qualquer dispositivo e plataforma. 

Incluído abaixo é uma arquitetura de alto nível e iIS Smooth Streaming Porting Kit box é a implementação do Cliente de streaming suave fornecida pela Microsoft e inclui toda a lógica principal para a reprodução de conteúdo de Streaming Suave. Este conteúdo é então portado por parceiros para um dispositivo ou plataforma específico, implementando interfaces apropriadas. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Description
A SSPK é licenciada em termos que oferecem excelente valor de negócio. A licença SSPK fornece à indústria:

* Fonte do Kit de Porting de Streaming Suave em C++ 
  * implementa a funcionalidade do Cliente de Streaming Suave
  * adiciona análise de formato, heurística, lógica de tampão, etc.
* APIs de aplicação de jogador 
  * interfaces de programação para interação com uma aplicação de leitor de media
* Interface de Camada de Abstração de Plataforma (PAL) 
  * interfaces de programação para interação com o sistema operativo (fios, tomadas)
* Interface de camada de abstração de hardware (HAL) 
  * interfaces de programação para interação com descodificadores A/V de hardware (descodificando, renderizando)
* Interface de Gestão de Direitos Digitais (DRM) 
  * interfaces de programação para manusear DRM através da Camada de Abstração DRM (DAL)
  * O Microsoft PlayReady Porting Kit envia separadamente, mas integra-se através desta interface. Para mais detalhes sobre o licenciamento do Dispositivo PlayReady do Microsoft, clique [aqui](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Amostras de implementação 
  * amostra de implementação PAL para Linux
  * implementação HAL da amostra para GStreamer

## <a name="licensing-options"></a>Opções de Licenciamento
O Microsoft Smooth Streaming Client Porting Kit está disponível para licenciados ao abrigo de dois acordos de licença distintos: um para o desenvolvimento de Produtos Provisórios de Clientes de Streaming Suave e outro para distribuir Produtos Finais de Cliente de Streaming Suave para os utilizadores finais.

* Para fabricantes de chipsets, integradores de sistemas ou fornecedores independentes de software (ISVs) que necessitem de um kit de porção de código fonte para desenvolver produtos provisórios, deve ser executado um Kit Dedeting Do Kit Provisório do Microsoft Smooth Streaming Porting **Kit.**
* Para fabricantes de dispositivos ou ISVs que necessitem de direitos de distribuição de produtos finais do cliente de streaming suave para os utilizadores finais, a Licença final de **produto do** Microsoft Smooth Streaming Client Kit deve ser executada.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Cliente Porting Kit Licença provisória de produto
Ao abrigo desta licença, a Microsoft oferece um Kit de Porting de Cliente de Streaming Suave e os direitos de propriedade intelectual necessários para desenvolver e distribuir produtos provisórios de clientes de streaming suave para outros licenciados de kit de porting de cliente de streaming suave que distribuem Produtos Finais do Cliente de Streaming Suave.

#### <a name="fee-structure"></a>Estrutura de taxas
Uma taxa única de licença de $50.000 dá acesso ao Kit de Porting do Cliente de Streaming Suave. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming Cliente Porting Kit Final Product License
Ao abrigo desta licença, a Microsoft oferece todos os direitos de propriedade intelectual necessários para receber produtos provisórios do cliente de streaming suave de outros licenciados do Kit de Porting do Cliente de Streaming Suave e para distribuir produtos finais de clientes de streaming suave de marca da empresa aos utilizadores finais.

#### <a name="fee-structure"></a>Estrutura de taxas
O Produto Final do Cliente de Streaming Suave é oferecido sob um modelo de royalties como em:

* $0,10 por implementação do dispositivo enviado
* A realeza está limitada a $50.000 por ano.
* Nenhuma realeza para as primeiras 10.000 implementações de dispositivos por ano 

## <a name="licensing-procedure-and-sspk-access"></a>Procedimento de licenciamento e acesso SSPK
E-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) para todas as consultas de licenciamento.

O portal de distribuição SSPK está acessível a licenciados provisórios registados.

Os licenciados provisórios e finais da SSPK podem submeter perguntas técnicas a [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com) .

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming Cliente Contrato de Produto Provisório Licenciados

* Adroit Business Solutions, Inc
* Difusão Digital Avançada SA
* AirTies Kablosuz Iletismo Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Corporação Alticast
* Amazon Digital Services, Inc.
* Tecnologia Arion, Inc.
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
* Açafrão Digital Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Corporação Sony
* Tatung Technology Inc.
* Top Victory Investments, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* Corporação ZTE

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming Cliente Final Product Agreement Licensees
* Difusão Digital Avançada SA
* AirTies Kablosuz Iletismo Sanayive Dis Ticaret A.S.
* AmTRAN Technology Co., Ltd 
* Arcadyan Technology Corporation
* Arcelik A.S.
* Compal Electronics, Inc.
* TECNOLOGIA EXPRESS LUCK LIMITED
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Hisense International Co., Ltd.
* Hisense Visual Technology Co., Ltd
* HKC Corporation Limited
* Hong Kong Konka Ltd
* Corporação Innolux
* Jinpin Electric Company Ltd.Zhuhai.S.E.Z
* K-Tronics (Suzhou) Technology Co., Ltd. 
* Kaonmedia Co., Ltd.
* Corporação KDDI
* Mega Fame Electronics Co. Limited
* MIRC Electronics Limited
* MOKA INTERNATIONAL LIMITED
* Nintendo Co., Ltd.
* ONEPLUS ELECTRONICS (SHENZHEN) CO., LTD.
* Corporação Panasonic
* Qingdao Haier Optronics Co., Ltd.
* Shenzhen ATEKO PHOTO Electricity Co.,Ltd.
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen KTC Technology Co., Ltd. 
* Shenzhen Maxmade Technology Co., Ltd
* Shenzhen MTC Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Corpo Industrial de Skardin.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SMARDTV GLOBAL SAS
* SoftAtHome
* Corporação Sony
* Tecnologias de Entrega Technicolor, SAS
* Top Victory Investments, Ltd.
* UMC Polónia sp. z .o.o.
* Vizio, Inc.
* Corporação ZTE

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

