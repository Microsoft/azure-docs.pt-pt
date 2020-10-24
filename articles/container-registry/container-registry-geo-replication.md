---
title: Georreplicação de um registo
description: Começa a criar e gerir um registo geo-replicado de contentores Azure, que permite ao registo servir várias regiões com réplicas regionais multi-master. A geo-replicação é uma característica do nível de serviço Premium.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: a26a3a0902b76359dc7441d97fa2516989ec7f0b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486877"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replicação no Registo do Contentor de Azure

As empresas que querem uma presença local, ou um backup quente, optam por executar serviços de várias regiões do Azure. Como melhor prática, a colocação de um registo de contentor em cada região onde as imagens são executadas possibilitam operações perto da rede que, por sua vez, permitem transferências de camadas de imagens rápidas e fiáveis. A georreplicação permite que um registo de contentor do Azure funcione como um registo único, o qual serve várias regiões com vários registos multimestre regionais. 

Um registo com georreplicação proporciona as seguintes vantagens:

* Nomes de registo/imagem/etiqueta únicos podem ser utilizados em várias regiões
* Acesso de registo perto da rede a partir de implementações regionais
* Nenhum honorário de saída adicional, pois as imagens são obtidas a partir de um registo local replicado na mesma região que o anfitrião do contentor
* Gestão única de um registo em múltiplas regiões

> [!NOTE]
> Se necessitar de manter cópias de imagens de contentores em mais de um registo de contentores Azure, o Registo de Contentores Azure também suporta [a importação de imagens](container-registry-import-images.md). Por exemplo, num fluxo de trabalho de DevOps, você pode importar uma imagem de um registo de desenvolvimento para um registo de produção, sem precisar de usar comandos Docker.
>

## <a name="example-use-case"></a>Caso de uso de exemplo
A Contoso gere um site de presença pública localizado nos EUA, Canadá e Europa. Para servir estes mercados com conteúdo local e de proximidade de rede, a Contoso gere clusters [Azure Kubernetes Service](../aks/index.yml) (AKS) nos EUA Ocidentais, Leste dos EUA, Canadá Central e Europa Ocidental. A aplicação do site, implantada como uma imagem do Docker, utiliza o mesmo código e imagem em todas as regiões. O conteúdo, local daquela região, é recuperado de uma base de dados, que é a provisionada de forma única em cada região. Cada implantação regional tem a sua configuração única para recursos como a base de dados local.

A equipa de desenvolvimento está localizada em Seattle WA, utilizando o centro de dados dos EUA.

![Empurrando para vários registos](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Empurrando para vários registos*

Antes de usar as funcionalidades de geo-replicação, Contoso tinha um registo baseado nos EUA no Oeste dos EUA, com um registo adicional na Europa Ocidental. Para servir estas diferentes regiões, a equipa de desenvolvimento empurrou imagens para dois registos diferentes.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Puxando de vários registos](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Puxando de vários registos*

Os desafios típicos de vários registos incluem:

* Os aglomerados central dos EUA, Oeste e Canadá retiram-se todos do registo dos EUA ocidentais, incorrendo em taxas de saída à medida que cada um destes anfitriões de contentores remotos retira imagens dos centros de dados dos EUA Ocidentais.
* A equipa de desenvolvimento tem de levar imagens aos registos dos EUA e da Europa Ocidental.
* A equipa de desenvolvimento deve configurar e manter cada implantação regional com nomes de imagem referentes ao registo local.
* O acesso ao registo deve ser configurado para cada região.

## <a name="benefits-of-geo-replication"></a>Benefícios da geo-replicação

![Puxando de um registo geo-replicado](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Utilizando a funcionalidade de geo-replicação do Registo do Contentor Azure, estes benefícios são realizados:

* Gerir um registo único em todas as regiões: `contoso.azurecr.io`
* Gerir uma única configuração de implementações de imagem como todas as regiões utilizaram o mesmo URL de imagem: `contoso.azurecr.io/public/products/web:1.2`
* Empurre para um único registo, enquanto a ACR gere a geo-replicação. Pode configurar [webhooks](container-registry-webhook.md) regionais para notificá-lo de eventos em réplicas específicas.

## <a name="configure-geo-replication"></a>Configurar georreplicação

Configurar a geo-replicação é tão fácil como clicar em regiões num mapa. Também pode gerir a geo-replicação utilizando ferramentas, incluindo os comandos [de replicação az acr](/cli/azure/acr/replication) no CLI Azure, ou implementar um registo ativado para a geo-replicação com um [modelo de Gestor de Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

A geo-replicação é uma característica dos [registos Premium.](container-registry-skus.md) Se o seu registo ainda não for Premium, pode mudar de Básico e Standard para Premium no [portal Azure:](https://portal.azure.com)

![Mudar os níveis de serviço no portal Azure](media/container-registry-skus/update-registry-sku.png)

Para configurar a geo-replicação para o seu registo Premium, faça login no portal Azure em https://portal.azure.com .

Navegue para o seu Registo de Contentores Azure e selecione **Replicações:**

![Replicações na IU de registo de contentor do portal do Azure](media/container-registry-geo-replication/registry-services.png)

É apresentado um mapa que mostra todas as regiões atuais do Azure:

 ![Mapa de região no portal do Azure](media/container-registry-geo-replication/registry-geo-map.png)

* Hexágonos azuis representam réplicas atuais
* Hexágonos verdes representam possíveis regiões réplicas
* Hexágonos cinzentos representam regiões do Azure ainda não disponíveis para replicação

Para configurar uma réplica, selecione um hexágono verde e, em seguida, **selecione Criar**:

 ![Criar a IU de replicação no portal do Azure](media/container-registry-geo-replication/create-replication.png)

Para configurar réplicas adicionais, selecione os hexágonos verdes para outras regiões e, em seguida, clique em **Criar**.

O ACR começa a sincronizar imagens através das réplicas configuradas. Uma vez concluído, o portal reflete *Ready*. O estado de réplica no portal não atualiza automaticamente. Utilize o botão de atualização para ver o estado atualizado.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Considerações para a utilização de um registo geo-replicado

* Cada região num registo geo-replicado é independente uma vez criada. As SLAs do Registo de Contentores Azure aplicam-se a cada região geo-replicada.
* Ao empurrar ou retirar imagens de um registo geo-replicado, o Azure Traffic Manager em segundo plano envia o pedido para o registo localizado na região que lhe é mais próximo em termos de latência da rede.
* Depois de empurrar uma atualização de imagem ou etiqueta para a região mais próxima, leva algum tempo para o Registo do Contentor de Azure replicar os manifestos e camadas para as restantes regiões em que optou. Imagens maiores demoram mais tempo a replicar-se do que as mais pequenas. As imagens e tags são sincronizadas nas regiões de replicação com um modelo de consistência eventual.
* Para gerir fluxos de trabalho que dependem de atualizações push para um registo geo-replicado, recomendamos que configures [webhooks](container-registry-webhook.md) para responder aos eventos push. Você pode configurar webhooks regionais dentro de um registro geo-replicado para rastrear eventos push à medida que eles completam em todas as regiões geo-replicadas.
* Para servir bolhas que representam camadas de conteúdo, o Registo do Contentor Azure utiliza pontos finais de dados. Pode ativar [pontos finais de dados dedicados](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) para o seu registo em cada uma das regiões geo-replicadas do seu registo. Estes pontos finais permitem a configuração de regras de acesso a firewall rigorosamente rigorosamente ateadas. Para efeitos de resolução de problemas, pode [desativar opcionalmente o encaminhamento para uma replicação,](#temporarily-disable-routing-to-replication) mantendo os dados replicados.
* Se configurar um [link privado](container-registry-private-link.md) para o seu registo utilizando pontos finais privados numa rede virtual, os pontos finais de dados dedicados em cada uma das regiões geo-replicadas são ativados por padrão. 

## <a name="delete-a-replica"></a>Eliminar réplicas

Depois de configurar uma réplica para o seu registo, pode eliminá-la a qualquer momento se já não for necessária. Elimine uma réplica utilizando o portal Azure ou outras ferramentas, como o comando [de eliminação de replicação az acr](/cli/azure/acr/replication#az-acr-replication-delete) no CLI Azure.

Para eliminar uma réplica no portal Azure:

1. Navegue para o seu Registo de Contentores Azure e selecione **Replications**.
1. Selecione o nome de uma réplica e selecione **Delete**. Confirme se pretende eliminar a réplica.

Para utilizar o CLI Azure para eliminar uma réplica da *miogrímica* na região leste dos EUA:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Preços de geo-replicação

A geo-replicação é uma característica do [nível](container-registry-skus.md) de serviço Premium do Registo de Contentores Azure. Quando replica um registo nas regiões desejadas, incorre em taxas de registo Premium para cada região.

No exemplo anterior, Contoso consolidou dois registos para um, adicionando réplicas aos EUA, Canadá Central e Europa Ocidental. A Contoso pagaria quatro vezes Premium por mês, sem nenhuma configuração ou gestão adicional. Cada região agora puxa as suas imagens localmente, melhorando o desempenho, fiabilidade sem taxas de erupção de rede do Oeste dos EUA para o Canadá e Leste dos EUA.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Operações de pressão de resolução de problemas com registos geo-replicados
 
Um cliente Docker que empurra uma imagem para um registo geo-replicado pode não empurrar todas as camadas de imagem e o seu manifesto para uma única região replicada. Isto pode ocorrer porque o Azure Traffic Manager encaminha pedidos de registo para o registo replicado mais próximo da rede. Se o registo tiver duas regiões de replicação *próximas,* as camadas de imagem e o manifesto podem ser distribuídos pelos dois locais, e a operação de pressão falha quando o manifesto é validado. Este problema ocorre devido à forma como o nome DNS do registo é resolvido em alguns anfitriões do Linux. Este problema não ocorre no Windows, que fornece uma cache DNS do lado do cliente.
 
Se este problema ocorrer, uma solução é aplicar uma cache DNS do lado do cliente, como `dnsmasq` no anfitrião Linux. Isto ajuda a garantir que o nome do registo seja resolvido de forma consistente. Se estiver a utilizar um Linux VM em Azure para empurrar para um registo, consulte as opções de [resolução de nomes DNS para máquinas virtuais Linux em Azure](../virtual-machines/linux/azure-dns.md).

Para otimizar a resolução de DNS à réplica mais próxima ao empurrar imagens, configurar um registo geo-replicado nas mesmas regiões do Azure como a fonte das operações push, ou a região mais próxima quando trabalha fora de Azure.

### <a name="temporarily-disable-routing-to-replication"></a>Desativar temporariamente o encaminhamento para a replicação

Para resolver problemas com um registo geo-replicado, é melhor desativar temporariamente o encaminhamento do Gestor de Tráfego para uma ou mais replicações. A partir da versão 2.8 do Azure CLI, pode configurar uma `--region-endpoint-enabled` opção (pré-visualização) quando criar ou atualizar uma região replicada. Quando define a opção de uma replicação para, o Gestor de `--region-endpoint-enabled` Tráfego já não `false` encaminha o docker push ou puxa os pedidos para aquela região. Por predefinição, o encaminhamento para todas as replicações está ativado e a sincronização de dados em todas as replicações ocorre se o encaminhamento está ativado ou desativado.

Para desativar o encaminhamento para uma replicação existente, primeiro executar [a lista de replicações az acr][az-acr-replication-list] para listar as replicações no registo. Em seguida, executar [a atualização de replicação az acr][az-acr-replication-update] e definir `--region-endpoint-enabled false` para uma replicação específica. Por exemplo, para configurar o cenário para a replicação *do westus* no *miogresso:*

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Para restaurar o encaminhamento para uma replicação:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Passos seguintes

Confira a série tutorial em três partes, [geo-replicação no Registo de Contentores Azure.](container-registry-tutorial-prepare-registry.md) Caminhe através da criação de um registo geo-replicado, construindo um contentor e, em seguida, implantando-o com um único `docker push` comando para várias aplicações web regionais para instâncias de contentores.

> [!div class="nextstepaction"]
> [Geo-replicação no Registo do Contentor de Azure](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
