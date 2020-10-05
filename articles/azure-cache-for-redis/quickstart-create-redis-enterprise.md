---
title: 'Quickstart: Criar uma cache de nível Enterprise'
description: Neste quickstart, aprenda a criar um exemplo de Azure Cache para o nível Redis Enterprise
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/12/2020
ms.openlocfilehash: 3df6cb1afe8a6249eadbbd3f61619e66be2d2478
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "83402498"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Quickstart: Criar uma cache de nível Enterprise (pré-visualização)

Azure Cache para os níveis da Redis Enterprise fornecem a [Redis Enterprise](https://redislabs.com/redis-enterprise/) totalmente integrada e gerida em Azure. Estão disponíveis como pré-visualização. Há dois novos níveis nesta pré-visualização:
* Enterprise, que usa memória volátil (DRAM) numa máquina virtual para armazenar dados
* Enterprise SSD, que usa memória volátil e não volátil (NVMe) para armazenar dados.

Não há qualquer custo para se juntar à pré-estreia. Registe-se através do [Azure Marketplace](https://aka.ms/redispreviewsignup/) **Contacte-me** se estiver interessado. Temos um número muito limitado de pontos de participante e não podemos garantir que será aceite na pré-visualização.

## <a name="prerequisites"></a>Pré-requisitos

Vai precisar de uma assinatura Azure antes de começar. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) primeiro.

## <a name="create-a-cache"></a>Criar uma cache
1. Para criar uma cache, inscreva-se no portal Azure utilizando o link no seu convite de pré-visualização e selecione **Criar um recurso**.

   > [!IMPORTANT] 
   > Não subscreva a *Azure Cache para Redis, Enterprise Tiers* no Marketplace diretamente.
   > Este passo é realizado automaticamente pela Cache Azure para o portal Redis UI.
   >
   
1. Na página **Nova,** selecione **Bases de Dados** e, em seguida, selecione **Azure Cache para Redis**.
   
   ![Selecione Azure Cache para Redis](media/cache-create/new-cache-menu.png)
   
1. Na página **New Redis Cache,** configufique as definições para o seu novo cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu cache * \<DNS name> <Azure region> será. . . redisenterprise.cache.azure.net.* | 
   | **Localização** | Desça e selecione um local. | Os níveis empresariais estão disponíveis nos EUA Ocidentais, Leste dos EUA 2 e Europa Ocidental. |
   | **Nível cache** | Desça e selecione um nível *Enterprise DRAM* ou *Enterprise SSD* e um tamanho. |  O nível determina o tamanho, desempenho e funcionalidades que estão disponíveis para a cache. |
   
   ![Básicos de nível da empresa](media/cache-create/enterprise-tier-basics.png) 

1. Selecione **Seguinte: Networking** e saltar.

   > [!NOTE] 
   > O apoio à ligação privada virá mais tarde.
   >

1. Selecione **Seguinte: Avançado**.
   
   Pode manter as definições predefinidos ou alterá-las conforme apropriado. Ao **ligar, permita o acesso apenas através do TLS,** tem de utilizar o TLS para aceder à nova cache a partir da sua aplicação.

   ![Nível de empresa avançado](media/cache-create/enterprise-tier-advanced.png) 

   > [!NOTE] 
   > Os Módulos Redis ainda não são suportados no nível SSD da Enterprise. Se planeia utilizar um Módulo Redis, não se esqueça de escolher uma cache de nível Enterprise.
   >
   
1. Selecione **Seguinte: Etiquetas** e saltar.

1. Selecione **Seguinte: Resumo**.

   ![Resumo do nível da empresa](media/cache-create/enterprise-tier-summary.png) 

1. Clique na caixa de verificação em **Termos,** reveja as definições e, em seguida, selecione **Review + create**.
   
   Leva algum tempo para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada.

   > [!NOTE] 
   > Algum tempo depois da criação da cache de nível Enterprise, receberá um e-mail **da Azure** Marketplace para configurar *a Azure Cache para Redis, Enterprise Tiers*. Esta ação não é necessária. Pode ignorar com segurança esse e-mail.
   >

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma instância de nível empresarial de Azure Cache para Redis.

> [!div class="nextstepaction"]
> [Crie uma aplicação web ASP.NET que utilize um Cache Azure para Redis.](./cache-web-app-howto.md)

