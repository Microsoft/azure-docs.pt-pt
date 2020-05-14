---
title: 'Quickstart: Criar uma cache de nível Enterprise'
description: Neste arranque rápido, aprenda a criar uma instância de Azure Cache para redis enterprise tier
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/12/2020
ms.openlocfilehash: f3eee85c600d40b3997a0e6dff6a9b218242feb4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83204719"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Quickstart: Criar uma cache de nível Enterprise (pré-visualização)

A Azure Cache para os níveis da Redis Enterprise fornece a [Redis Enterprise](https://redislabs.com/redis-enterprise/) totalmente integrada e gerida em Azure. Estão disponíveis como pré-estreia. Há dois novos níveis nesta pré-visualização:
* Enterprise, que usa memória volátil (DRAM) numa máquina virtual para armazenar dados
* Enterprise SSD, que utiliza memória volátil e não volátil (NVMe) para armazenar dados.

Não há nenhum custo para se juntar à pré-estreia. Registe-se no [Azure Marketplace,](https://aka.ms/redispreviewsignup/) se estiver interessado. Temos um número muito limitado de lugares de participantes e não podemos garantir que será aceite na pré-visualização.

## <a name="prerequisites"></a>Pré-requisitos

Vai precisar de uma assinatura Azure antes de começar. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) primeiro.

## <a name="create-a-cache"></a>Criar uma cache
1. Para criar uma cache, inscreva-se no portal Azure utilizando o link no seu convite de pré-visualização e selecione **Criar um recurso**.

   > [!IMPORTANT] 
   > Não subscreva *a Azure Cache para redis, Enterprise Tiers* no Marketplace diretamente.
   > Este passo é realizado pelo Azure Cache para redis portal UI automaticamente.
   >
   
1. Na **página Nova,** selecione Bases de **Dados** e, em seguida, selecione **Azure Cache para Redis**.
   
   ![Selecione Azure Cache para Redis](media/cache-create/new-cache-menu.png)
   
1. Na página **New Redis Cache,** configure as definições para a sua nova cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para redis exemplo. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou selecione **Criar novo** e insira um novo nome de grupo de recursos. | Nome para o grupo de recursos no qual criar a sua cache e outros recursos. Ao colocar todos os recursos da sua aplicação num só grupo de recursos, pode facilmente geri-los ou eliminá-los juntos. | 
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome do *anfitrião* da sua instância de cache será * \< o nome DNS <Azure region>>. . . redisenterprise.cache.azure.net.* | 
   | **Localização** | Desça e selecione um local. | Os níveis empresariais estão disponíveis nos EUA Ocidentais, Leste dos EUA 2 e Europa Ocidental. |
   | **Nível cache** | Desça e selecione um *dRAM da enterprise* ou um nível *SSD da Empresa* e um tamanho. |  O nível determina o tamanho, desempenho e características disponíveis para a cache. |
   
   ![Básicos de nível empresarial](media/cache-create/enterprise-tier-basics.png) 

1. Selecione **Seguinte: Networking** e skip.

   > [!NOTE] 
   > O apoio à ligação privada virá mais tarde.
   >

1. Selecione **Seguinte: Avançado**.
   
   Pode manter as definições predefinidas ou troterá-as conforme apropriado. Ao ligar O acesso permitir apenas através do **TLS,** deve utilizar o TLS para aceder à nova cache a partir da sua aplicação.

   ![Nível empresarial avançado](media/cache-create/enterprise-tier-advanced.png) 

   > [!NOTE] 
   > Os Módulos Redis ainda não são suportados no nível SSD da Enterprise. Se planeia utilizar um Módulo Redis, certifique-se de escolher uma cache de nível Enterprise.
   >
   
1. Selecione **Seguinte: Etiquetas** e saltar.

1. Selecione **Seguinte: Resumo**.

   ![Resumo do nível da empresa](media/cache-create/enterprise-tier-summary.png) 

1. Clique na caixa de verificação em **Termos,** reveja as definições e, em seguida, selecione **Rever + criar**.
   
   Leva algum tempo para a cache criar. Pode monitorizar o progresso na página azure cache para redis **overview.** Quando **o Estado** aparecer como **Funcionamento,** a cache está pronta a ser utilizada.

   > [!NOTE] 
   > Algum tempo depois da criação da cache de nível Enterprise, receberá um e-mail **de Ação exigido** do Azure Marketplace para configurar o *Azure Cache para Redis, Enterprise Tiers*. Esta ação não é necessária. Pode ignorar com segurança esse e-mail.
   >

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeste a criar uma instância de nível Empresarial de Azure Cache para redis.

> [!div class="nextstepaction"]
> [Crie uma ASP.NET aplicação web que utilize um Azure Cache para Redis.](./cache-web-app-howto.md)

