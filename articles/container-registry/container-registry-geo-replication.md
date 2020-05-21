---
title: Georreplicação de um registo
description: Começar a criar e gerir um registo de contentores Azure geo-replicado, que permite ao registo servir várias regiões com réplicas regionais multi-master.
author: stevelas
ms.topic: article
ms.date: 05/11/2020
ms.author: stevelas
ms.openlocfilehash: bea71695c66c77a8e9fff3cb708113a04f24ed96
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711572"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replicação no Registo de Contentores de Azure

As empresas que desejam uma presença local, ou um backup quente, optam por gerir serviços de várias regiões do Azure. Como melhor prática, a colocação de um registo de contentor em cada região onde as imagens são executadas possibilitam operações perto da rede que, por sua vez, permitem transferências de camadas de imagens rápidas e fiáveis. A georreplicação permite que um registo de contentor do Azure funcione como um registo único, o qual serve várias regiões com vários registos multimestre regionais. 

Um registo com georreplicação proporciona as seguintes vantagens:

* Nomes de registo/imagem/etiqueta únicos podem ser utilizados em várias regiões
* Acesso de registo perto da rede a partir de implementações regionais
* Nenhum honorário de saída adicional, pois as imagens são obtidas a partir de um registo local replicado na mesma região que o anfitrião do contentor
* Gestão única de um registo por várias regiões

> [!NOTE]
> Se necessitar de manter cópias de imagens de contentores em mais de um registo de contentores Azure, o Registo de Contentores Azure também suporta a importação de [imagem.](container-registry-import-images.md) Por exemplo, num fluxo de trabalho da DevOps, pode importar uma imagem de um registo de desenvolvimento para um registo de produção, sem precisar de usar comandos Docker.
>

## <a name="example-use-case"></a>Caso de utilização de exemplo
Contoso gere um site de presença pública localizado nos EUA, Canadá e Europa. Para servir estes mercados com conteúdo local e de proximidade de rede, contoso gere clusters [azure Kubernetes Service](/azure/aks/) (AKS) em West US, East US, Canada Central e West Europe. A aplicação do site, implementada como uma imagem do Docker, utiliza o mesmo código e imagem em todas as regiões. O conteúdo, local daquela região, é recuperado de uma base de dados, que é aprovisionada exclusivamente em cada região. Cada implantação regional tem a sua configuração única para recursos como a base de dados local.

A equipa de desenvolvimento está localizada em Seattle WA, utilizando o centro de dados dos EUA Ocidental.

![Empurrando para vários registos](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Empurrando para vários registos*

Antes de usar as características de geo-replicação, Contoso tinha um registo baseado nos EUA, com um registo adicional na Europa Ocidental. Para servir estas diferentes regiões, a equipa de desenvolvimento empurrou imagens para dois registos diferentes.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Puxando de vários registos](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Puxando de vários registos*

Os desafios típicos de vários registos incluem:

* Os aglomerados leste dos EUA, Oeste dos EUA e Canadá retiram todos do registo dos EUA Ocidentais, incorrendo em taxas de egress à medida que cada um destes anfitriões remotos de contentores retira imagens dos centros de dados dos EUA Ocidentais.
* A equipa de desenvolvimento tem de levar imagens para os registos dos EUA Ocidentais e da Europa Ocidental.
* A equipa de desenvolvimento deve configurar e manter cada implantação regional com nomes de imagem referentes ao registo local.
* O acesso ao registo deve ser configurado para cada região.

## <a name="benefits-of-geo-replication"></a>Benefícios da geo-replicação

![Puxando de um registo geo-replicado](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Utilizando a característica de geo-replicação do Registo de Contentores Azure, estes benefícios são realizados:

* Gerir um único registo em todas as regiões:`contoso.azurecr.io`
* Gerir uma única configuração de implementações de imagem, uma vez que todas as regiões utilizaram o mesmo URL de imagem:`contoso.azurecr.io/public/products/web:1.2`
* Empurre para um único registo, enquanto a ACR gere a geo-replicação. Pode configurar [webhooks](container-registry-webhook.md) regionais para notificá-lo de eventos em réplicas específicas.

## <a name="configure-geo-replication"></a>Configurar georreplicação

Configurar a geo-replicação é tão fácil como clicar em regiões num mapa. Também pode gerir a geo-replicação utilizando ferramentas, incluindo os comandos de [replicação az acr](/cli/azure/acr/replication) no ClI Azure, ou implementar um registo habilitado para a geo-replicação com um modelo de Gestor de [Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

A geo-replicação é uma característica dos [registos Premium.](container-registry-skus.md) Se o seu registo ainda não for Premium, pode mudar de Básico e Standard para Premium no [portal Azure:](https://portal.azure.com)

![Mudar os níveis de serviço no portal Azure](media/container-registry-skus/update-registry-sku.png)

Para configurar a geo-replicação para o seu registo Premium, inicie sessão no portal Azure em https://portal.azure.com .

Navegue para o seu Registo de Contentores Azure e selecione **Replicações:**

![Replicações na IU de registo de contentor do portal do Azure](media/container-registry-geo-replication/registry-services.png)

É exibido um mapa que mostra todas as regiões atuais de Azure:

 ![Mapa de região no portal do Azure](media/container-registry-geo-replication/registry-geo-map.png)

* Hexágonos azuis representam réplicas atuais
* Hexágonos verdes representam possíveis regiões réplicas
* Hexágonos cinzentos representam regiões de Azure ainda não disponíveis para replicação

Para configurar uma réplica, selecione um hexágono verde e, em seguida, **selecione Criar:**

 ![Criar a IU de replicação no portal do Azure](media/container-registry-geo-replication/create-replication.png)

Para configurar réplicas adicionais, selecione os hexágonos verdes para outras regiões e, em seguida, clique em **Criar**.

O ACR começa a sincronizar imagens através das réplicas configuradas. Uma vez concluído, o portal reflete *Ready*. O estado da réplica no portal não atualiza automaticamente. Utilize o botão de atualização para ver o estado atualizado.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Considerações para a utilização de um registo geo-replicado

* Cada região num registo geo-replicado é independente uma vez criada. As SLAs do Registo de Contentores Azure aplicam-se a cada região geo-replicada.
* Quando empurra ou puxa imagens de um registo geo-replicado, o Gestor de Tráfego azure em segundo plano envia o pedido para o registo localizado na região que lhe é mais próximo em termos de latência da rede.
* Depois de empurrar uma atualização de imagem ou etiqueta para a região mais próxima, leva algum tempo para o Registo de Contentores Azure replicar os manifestos e camadas para as restantes regiões em que optou. Imagens maiores demoram mais tempo a replicar-se do que as mais pequenas. Imagens e tags são sincronizadas em todas as regiões de replicação com um eventual modelo de consistência.
* Para gerir fluxos de trabalho que dependem de atualizações push para um registo geo-replicado, recomendamos que configure [webhooks](container-registry-webhook.md) para responder aos eventos push. Você pode configurar webhooks regionais dentro de um registo geo-replicado para rastrear eventos push à medida que eles completam em todas as regiões geo-replicadas.
* Para servir bolhas que representam camadas de conteúdo, o Azure Container Registy utiliza pontos finais de dados. Pode ativar [pontos finais dedicados](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) para o seu registo em cada uma das regiões geo-replicadas do seu registo. Estes pontos finais permitem a configuração de regras de acesso a firewall com um alcance apertado.
* Se configurar um [link privado](container-registry-private-link.md) para o seu registo utilizando pontos finais privados numa rede virtual, os pontos finais dedicados em cada uma das regiões geo-replicadas são ativados por padrão. 

## <a name="delete-a-replica"></a>Eliminar réplicas

Depois de configurar uma réplica para o seu registo, pode apagá-la a qualquer momento se já não for necessário. Elimine uma réplica utilizando o portal Azure ou outras ferramentas, como a [replicação az acr, apague](/cli/azure/acr/replication#az-acr-replication-delete) o comando no Azure CLI.

Para apagar uma réplica no portal Azure:

1. Navegue para o registo de contentores Azure e selecione **Replicações**.
1. Selecione o nome de uma réplica e selecione **Eliminar**. Confirme que pretende apagar a réplica.

Utilizar o Azure CLI para eliminar uma réplica do *meu registo* na região leste dos EUA:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Preços de geo-replicação

A geo-replicação é uma característica do [nível](container-registry-skus.md) de serviço Premium do Registo de Contentores Azure. Quando replica um registo para as regiões desejadas, incorre em taxas de registo Premium para cada região.

No exemplo anterior, Contoso consolidou dois registos para um, adicionando réplicas aos EUA Orientais, Canadá Central e Europa Ocidental. Contoso pagaria quatro vezes Premium por mês, sem configuração ou gestão adicional. Cada região agora puxa as suas imagens localmente, melhorando o desempenho, a fiabilidade sem taxas de egress de rede dos EUA Ocidentais para o Canadá e Leste dos EUA.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Operações de push de resolução de problemas com registos geo-replicados
 
Um cliente Docker que empurra uma imagem para um registo geo-replicado pode não empurrar todas as camadas de imagem e o seu manifesto para uma única região replicada. Isto pode ocorrer porque o Gestor de Tráfego Azure encaminha os pedidos de registo para o registo replicado mais próximo da rede. Se o registo tiver duas regiões de replicação *próximas,* camadas de imagem e o manifesto podem ser distribuídos para os dois locais, e a operação de impulso falha quando o manifesto é validado. Este problema ocorre devido à forma como o nome DNS do registo é resolvido em alguns anfitriões linux. Este problema não ocorre no Windows, que fornece uma cache DNS do lado do cliente.
 
Se este problema ocorrer, uma solução é aplicar uma cache DNS do lado do cliente, como `dnsmasq` no hospedeiro Linux. Isto ajuda a garantir que o nome do registo seja resolvido de forma consistente. Se estiver a usar um Linux VM em Azure para empurrar para um registo, consulte opções nas opções de Resolução de [Nomes DNS para máquinas virtuais Linux em Azure](../virtual-machines/linux/azure-dns.md).

Para otimizar a resolução do DNS para a réplica mais próxima ao empurrar imagens, configure um registo geo-replicado nas mesmas regiões de Azure como a fonte das operações de impulso, ou a região mais próxima quando trabalhar fora de Azure.

## <a name="next-steps"></a>Próximos passos

Confira a série tutorial em três partes, [geo-replicação no Registo de Contentores Azure](container-registry-tutorial-prepare-registry.md). Caminhe através da criação de um registo geo-replicado, construindo um contentor, e, em seguida, implantando-o com um único `docker push` comando para várias aplicações regionais para web apps para instâncias de contentores.

> [!div class="nextstepaction"]
> [Geo-replicação no Registo de Contentores de Azure](container-registry-tutorial-prepare-registry.md)
