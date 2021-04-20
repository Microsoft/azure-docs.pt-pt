---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 958c40ed311ebe8b2a83ce0d4d394d00f48d1afa
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732622"
---
Navegue para a conta de armazenamento para a qual gostaria de criar um ponto final privado. Na tabela de conteúdos para a conta de armazenamento, **selecione Networking,** **Private endpoint connections**, e, em seguida, **+ Private endpoint** para criar um novo ponto final privado. 

[![Uma imagem do item de ligações de ponto final privado na tabela de contas de armazenamento de conteúdos](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

O assistente resultante tem várias páginas para completar.

Na lâmina **Basics,** selecione o grupo de recursos, nome e região pretendidos para o seu ponto final privado. Estes podem ser o que quiser, eles não têm que combinar a conta de armazenamento de qualquer forma, embora você deve criar o ponto final privado na mesma região que a rede virtual em que você deseja criar o ponto final privado em.

![Uma imagem da secção Basics na secção de ponto final privado create](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

Na lâmina **de recurso,** selecione o botão de rádio **para Ligar a um recurso Azure no meu diretório**. Sob **o tipo de recurso,** selecione **Microsoft.Storage/storageAcounts** para o tipo de recurso. O campo **De recursos** é a conta de armazenamento com a partilha de ficheiros Azure a que deseja ligar. O sub-recurso-alvo é **ficheiro**, uma vez que este é para Ficheiros Azure.

A lâmina **de configuração** permite-lhe selecionar a rede virtual específica e a sub-rede a que gostaria de adicionar o seu ponto final privado. Tem de selecionar uma sub-rede distinta da sub-rede que adicionou o seu ponto de terminação de serviço a cima. A lâmina de configuração também contém as informações para criar/atualizar a zona privada de DNS. Recomendamos a utilização da `privatelink.file.core.windows.net` zona padrão.

![Uma imagem da secção de Configuração](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Clique **em Rever + criar** para criar o ponto final privado. 
