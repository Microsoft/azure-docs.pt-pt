---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 5/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09723030f0da0252120f66f36347cad2e3a3546a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93376153"
---
Navegue para a conta de armazenamento para a qual gostaria de criar um ponto final privado. Na tabela de conteúdos para a conta de armazenamento, selecione **ligações de ponto final privados** e, em seguida, **+ ponto final privado** para criar um novo ponto final privado. 

[![Uma imagem do item de ligações de ponto final privado na tabela de contas de armazenamento de conteúdos](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

O assistente resultante tem várias páginas para completar.

Na lâmina **Basics,** selecione o grupo de recursos, nome e região pretendidos para o seu ponto final privado. Estes podem ser o que quiser, eles não têm que combinar a conta de armazenamento de qualquer forma, embora você deve criar o ponto final privado na mesma região que a rede virtual em que você deseja criar o ponto final privado em.

![Uma imagem da secção Basics na secção de ponto final privado create](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

Na lâmina **de recurso,** selecione o botão de rádio **para Ligar a um recurso Azure no meu diretório**. Sob **o tipo de recurso,** selecione **Microsoft.Storage/storageAcounts** para o tipo de recurso. O campo **De recursos** é a conta de armazenamento com a partilha de ficheiros Azure a que deseja ligar. O sub-recurso-alvo é **ficheiro**, uma vez que este é para Ficheiros Azure.

A lâmina **de configuração** permite-lhe selecionar a rede virtual específica e a sub-rede a que gostaria de adicionar o seu ponto final privado. Tem de selecionar uma sub-rede distinta da sub-rede que adicionou o seu ponto de terminação de serviço a cima. A lâmina de configuração também contém as informações para criar/atualizar a zona privada de DNS. Recomendamos a utilização da `privatelink.file.core.windows.net` zona padrão.

![Uma imagem da secção de Configuração](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Clique **em Rever + criar** para criar o ponto final privado. 
