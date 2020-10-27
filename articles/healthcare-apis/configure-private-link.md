---
title: Link privado para Azure API para FHIR
description: Este artigo descreve como criar um ponto final privado para a Azure API para serviços FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/12/2020
ms.author: matjazl
ms.openlocfilehash: 121ddcc013fbeef29c6d22cfbd273cfd3b8d3d98
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92559003"
---
# <a name="configure-private-link"></a>Configure a ligação privada

> [!IMPORTANT]
> Esta capacidade está disponível em pré-visualização pública, é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O link privado permite-lhe aceder a Azure API para FHIR através de um ponto final privado, uma interface de rede que o conecta de forma privada e segura utilizando um endereço IP privado a partir da sua rede virtual. Com um link privado, pode aceder aos nossos serviços de forma segura a partir do seu Vnet como um serviço de primeira festa sem ter de passar por um DNS público. Este artigo acompanha-o como criar, testar e gerir o seu ponto final privado para a Azure API para fHIR.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um ponto final privado, existem alguns recursos Azure que você precisará criar primeiro:

- Grupo de Recursos – O grupo de recursos Azure que conterá a rede virtual e o ponto final privado.
- Azure API para FHIR – O recurso FHIR que gostaria de colocar atrás de um ponto final privado.
- Rede Virtual – O VNet ao qual os serviços do seu cliente e Private Endpoint estarão ligados.

Para mais informações, consulte a [Documentação do Link Privado.](https://docs.microsoft.com/azure/private-link/)

## <a name="disable-public-network-access"></a>Desativar o acesso à rede pública

A criação de um ponto final privado para o seu recurso FHIR não desativa automaticamente o tráfego público para o mesmo. Para isso, terá de atualizar o seu recurso FHIR para definir uma nova propriedade de "acesso público" de "Habilitado" a "Desativado". Tenha cuidado ao desativar o acesso à rede pública, pois todos os pedidos ao seu serviço FHIR que não venham de um ponto final privado devidamente configurado serão negados. Só será permitido tráfego dos seus pontos finais privados.

![Desativar o acesso à rede pública](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Criar ponto final privado

Para criar um ponto final privado, um desenvolvedor com permissões RBAC no recurso FHIR pode usar o portal Azure [PowerShell,](https://docs.microsoft.com/azure/private-link/create-private-endpoint-powershell)ou [Azure CLI](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli). Este artigo acompanha-o através dos passos sobre a utilização do portal Azure. Recomenda-se a utilização do portal Azure, uma vez que automatiza a criação e configuração da Zona Privada de DNS. Pode fazer referência aos [Guias de Início Rápido de Ligação Privada](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal) para obter mais detalhes.

Há duas maneiras de criar um ponto final privado. O fluxo de aprovação automática permite que um utilizador que tenha permissões DE RBAC no recurso FHIR crie um ponto final privado sem necessidade de aprovação. O fluxo de aprovação manual permite que um utilizador sem permissões no recurso FHIR solicite um ponto final privado para ser aprovado pelos proprietários do recurso FHIR.

### <a name="auto-approval"></a>Aprovação automática

Certifique-se de que a região para o novo ponto final privado é a mesma que a região para a sua Rede Virtual. A região para o seu recurso FHIR pode ser diferente.

![Guia básico do portal Azure](media/private-link/private-link-portal2.png)

Para o Tipo de Recurso, procure e selecione "Microsoft.HealthcareApis/services". Para obter recursos, selecione o recurso FHIR. Para sub-recursos de destino, selecione "fhir".

![Separador de recursos do portal Azure](media/private-link/private-link-portal1.png)

Se não tiver uma Zona Privada de DNS existente, selecione "(New)privatelink.azurehealthcareapis.com". Se já tiver a sua Zona DE DNS privada configurada, pode selecioná-la na lista. Deve estar no formato de "privatelink.azurehealthcareapis.com".

![Separador de configuração do portal Azure](media/private-link/private-link-portal3.png)

Depois de concluída a implementação, pode voltar ao separador "Ligações de ponto final privado", no qual verá "Aprovado" como estado de ligação.

### <a name="manual-approval"></a>Aprovação Manual

Para aprovação manual, selecione a segunda opção em Recurso, "Conecte-se a um recurso Azure por ID ou pseudónimo de recurso". Para sub-recurso target, insira "fhir" como na Aprovação Automática.

![Aprovação Manual](media/private-link/private-link-manual.png)

Depois de concluída a implementação, pode voltar ao separador "Ligações de ponto final privado", no qual pode aprovar, rejeitar ou remover a sua ligação.

![Opções](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Teste ponto final privado

Para se certificar de que o seu servidor FHIR não está a receber tráfego público depois de desativar o acesso à rede pública, tente acertar no ponto final /metadados do seu servidor a partir do seu computador. Deve receber um 403 Proibido. Note que pode demorar até 5 minutos após a atualização do pavilhão de acesso à rede pública antes de o tráfego público ser bloqueado.

Para se certificar de que o seu ponto final privado pode enviar tráfego para o seu servidor:

1. Crie um VM que esteja ligado à rede virtual e sub-rede em que o seu ponto final privado esteja configurado. Para garantir que o seu tráfego a partir do VM está apenas usando a rede privada, pode desativar o tráfego de internet de saída através da regra NSG.
2. RDP no VM.
3. Tente atingir o ponto final /metadados do seu servidor FHIR a partir do VM, deve receber a declaração de capacidade como resposta.

## <a name="manage-private-endpoint"></a>Gerir o ponto final privado

### <a name="view"></a>Vista

Os Pontos Finais Privados e o NIC associado são visíveis no portal Azure a partir do grupo de recursos em que foram criados.

![Ver em recursos](media/private-link/private-link-view.png)

### <a name="delete"></a>Eliminar

Os pontos finais privados só podem ser eliminados do portal Azure através da lâmina de visão geral (como abaixo) ou através da opção Eliminar no separador "Ligações de ponto final privados" da rede (pré-visualização). Clicar no botão eliminar irá eliminar o ponto final privado e o NIC associado. Se eliminar todos os pontos finais privados do recurso FHIR e o acesso à rede pública for desativado, nenhum pedido chegará ao seu servidor FHIR. Todos os pontos finais privados devem ser eliminados do recurso FHIR antes que o recurso FHIR possa ser eliminado ou movido.

![Eliminar ponto de final privado](media/private-link/private-link-delete.png)
