---
title: 'Quickstart: Criar uma cache de nível Enterprise'
description: Neste quickstart, aprenda a criar um exemplo de Azure Cache para o nível Redis Enterprise
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: ca5bdf606b51b4019bd9e655d172733074febd39
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078020"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Quickstart: Criar uma cache de nível Enterprise (pré-visualização)

Azure Cache para os níveis da Redis Enterprise fornecem a [Redis Enterprise](https://redislabs.com/redis-enterprise/) totalmente integrada e gerida em Azure. Estão disponíveis como pré-visualização. Há dois novos níveis nesta pré-visualização:
* Enterprise, que usa memória volátil (DRAM) numa máquina virtual para armazenar dados
* Enterprise Flash, que utiliza memória volátil e não volátil (NVMe ou SSD) para armazenar dados.

## <a name="prerequisites"></a>Pré-requisitos

Vai precisar de uma assinatura Azure antes de começar. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) primeiro.

## <a name="create-a-cache"></a>Criar uma cache
1. Para criar uma cache, inscreva-se no portal Azure utilizando o link no seu convite de pré-visualização e selecione **Criar um recurso** .

1. Na página **Nova,** selecione **Bases de Dados** e, em seguida, selecione **Azure Cache para Redis** .
   
   ![Selecione Azure Cache para Redis](media/cache-create/new-cache-menu.png)
   
1. Na página **New Redis Cache,** configufique as definições para o seu novo cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu cache *\<DNS name> <Azure region> será. . . redisenterprise.cache.azure.net.* | 
   | **Localização** | Desça e selecione um local. | Os níveis de empresa estão disponíveis em regiões de Azure limitadas durante a pré-visualização. |
   | **Tipo cache** | Desça e selecione um nível *Enterprise* ou *Enterprise Flash* e um tamanho. |  O nível determina o tamanho, desempenho e funcionalidades que estão disponíveis para a cache. |
   
   ![Básicos de nível da empresa](media/cache-create/enterprise-tier-basics.png) 

   > [!NOTE] 
   > Certifique-se de verificar a caixa em "Termos" antes de prosseguir.
   >

1. Selecione **Seguinte: Networking** e saltar.

   > [!NOTE] 
   > A opção de ligação privada está a ser lançada e pode não estar disponível imediatamente na sua região.
   >

1. Selecione **Seguinte: Avançado** .
   
   Pode manter as definições predefinidos ou alterá-las conforme apropriado. Ao **ligar, permita o acesso apenas através do TLS,** tem de utilizar o TLS para aceder à nova cache a partir da sua aplicação.

   ![Nível de empresa avançado](media/cache-create/enterprise-tier-advanced.png) 

   > [!NOTE] 
   > Os Módulos Redis ainda não estão suportados no nível Enterprise Flash. Se planeia utilizar um Módulo Redis, não se esqueça de escolher uma cache de nível Enterprise.
   >
   
1. Selecione **Seguinte: Etiquetas** e saltar.

1. Selecione **Seguinte: Rever + criar** .

   ![Resumo do nível da empresa](media/cache-create/enterprise-tier-summary.png) 

1. Reveja as definições e clique em **Criar** .
   
   Leva algum tempo para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma instância de nível empresarial de Azure Cache para Redis.

> [!div class="nextstepaction"]
> [Crie uma aplicação web ASP.NET que utilize um Cache Azure para Redis.](./cache-web-app-howto.md)

