---
title: Link privado para Azure API para FHIR
description: Este artigo descreve como criar um ponto final privado para a Azure API para serviços FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/03/2021
ms.author: zxue
ms.openlocfilehash: 5e24c7666fd2ece7d284b7705bc481866d7604de
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097045"
---
# <a name="configure-private-link"></a>Configure a ligação privada

O link privado permite-lhe aceder a Azure API para FHIR através de um ponto final privado, uma interface de rede que o conecta de forma privada e segura utilizando um endereço IP privado a partir da sua rede virtual. Com um link privado, pode aceder aos nossos serviços de forma segura a partir do seu Vnet como um serviço de primeira festa sem ter de passar por um DNS público. Este artigo acompanha-o como criar, testar e gerir o seu ponto final privado para a Azure API para fHIR.

>[!Note]
>Nem o Private Link nem o API Azure para fHIR podem ser transferidos de um grupo de recursos ou subscrição para outro assim que o Private Link estiver ativado. Para se mover, elimine primeiro o Link Privado, em seguida, mova a AZure API para fHIR e crie um novo Link Privado uma vez que o movimento esteja completo. Avalie potenciais ramificações de segurança antes de eliminar o Private Link.
>
>Se os registos de auditoria de exportação e/métricas estiverem habilitados para a API Azure para fHIR, atualize a definição de exportação através de Definições de Diagnóstico a partir do portal.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um ponto final privado, existem alguns recursos Azure que você precisará criar primeiro:

- Grupo de Recursos – O grupo de recursos Azure que conterá a rede virtual e o ponto final privado.
- Azure API para FHIR – O recurso FHIR que gostaria de colocar atrás de um ponto final privado.
- Rede Virtual – O VNet ao qual os serviços do seu cliente e Private Endpoint estarão ligados.

Para mais informações, consulte a [Documentação do Link Privado.](../private-link/index.yml)

## <a name="disable-public-network-access"></a>Desativar o acesso à rede pública

A criação de um ponto final privado para o seu recurso FHIR não desativa automaticamente o tráfego público para o mesmo. Para isso, terá de atualizar o seu recurso FHIR para definir uma nova propriedade de "acesso público" de "Habilitado" a "Desativado". Tenha cuidado ao desativar o acesso à rede pública, pois todos os pedidos ao seu serviço FHIR que não venham de um ponto final privado devidamente configurado serão negados. Só será permitido tráfego dos seus pontos finais privados.

![Desativar o acesso à rede pública](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Criar ponto final privado

Para criar um ponto final privado, um desenvolvedor com permissões RBAC no recurso FHIR pode usar o portal Azure [PowerShell,](../private-link/create-private-endpoint-powershell.md)ou [Azure CLI](../private-link/create-private-endpoint-cli.md). Este artigo acompanha-o através dos passos sobre a utilização do portal Azure. Recomenda-se a utilização do portal Azure, uma vez que automatiza a criação e configuração da Zona Privada de DNS. Pode fazer referência aos [Guias de Início Rápido de Ligação Privada](../private-link/create-private-endpoint-portal.md) para obter mais detalhes.

Há duas maneiras de criar um ponto final privado. O fluxo de aprovação automática permite que um utilizador que tenha permissões DE RBAC no recurso FHIR crie um ponto final privado sem necessidade de aprovação. O fluxo de aprovação manual permite que um utilizador sem permissões no recurso FHIR solicite um ponto final privado para ser aprovado pelos proprietários do recurso FHIR.

### <a name="auto-approval"></a>Aprovação automática

Certifique-se de que a região para o novo ponto final privado é a mesma que a região para a sua Rede Virtual. A região para o seu recurso FHIR pode ser diferente.

![Guia básico do portal Azure](media/private-link/private-link-portal2.png)

Para o Tipo de Recurso, procure e selecione "Microsoft.HealthcareApis/services". Para obter recursos, selecione o recurso FHIR. Para sub-recursos de destino, selecione "fhir".

![Separador de recursos do portal Azure](media/private-link/private-link-portal1.png)

Se não tiver uma Zona Privada de DNS existente, selecione "(New)privatelink.azurehealthcareapapis.com". Se já tiver a sua Zona DE DNS privada configurada, pode selecioná-la na lista. Deve estar no formato de "privatelink.azurehealthcareapis.com".

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

Os pontos finais privados só podem ser eliminados do portal Azure através da lâmina de visão geral (como abaixo) ou através da opção Eliminar no separador "Ligações de ponto final privados" da rede (pré-visualização). Clicar no botão eliminar irá eliminar o ponto final privado e o NIC associado. Se eliminar todos os pontos finais privados do recurso FHIR e o acesso à rede pública for desativado, nenhum pedido chegará ao seu servidor FHIR.

![Eliminar ponto de final privado](media/private-link/private-link-delete.png)
