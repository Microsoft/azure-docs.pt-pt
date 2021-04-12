---
title: Erros comuns e questões conhecidas aquando da migração para a Azure Cloud Services (suporte alargado)
description: Visão geral dos erros comuns ao migrar dos Serviços cloud (clássico) para o Cloud Service (suporte alargado)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287040"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Erros comuns e questões conhecidas aquando da migração para a Azure Cloud Services (suporte alargado)

Este artigo abrange questões conhecidas e erros comuns que poderá encontrar quando a migração dos Serviços Cloud (clássicos) para os Serviços cloud (suporte alargado). 

## <a name="known-issues"></a>Problemas conhecidos
As questões que se seguem são conhecidas e abordadas.

| Problemas conhecidos | Mitigação | 
|---|---|
| Role Instances reiniciando UD por UD após compromisso bem sucedido. | A operação de reinício segue o mesmo método que os lançamentos mensais de SO de hóspedes. Não cometa migração de serviços na nuvem com uma única instância de função ou impactado pelo reinício.| 
| O portal Azure não pode ler o estado de migração após a atualização do navegador. | Rerun validar e preparar a operação para voltar ao estado de migração original. | 
| Certificado apresentado como recurso secreto no cofre da chave. | Após a migração, recarregue o certificado como recurso de certificado para simplificar a operação de atualização dos Serviços cloud (suporte alargado). | 
| As etiquetas de implantação não são guardadas como tags como parte da migração. | Crie manualmente as etiquetas após a migração para manter esta informação.
| O nome do Grupo de Recursos está em todas as tampas. | Sem impacto. Solução ainda não disponível. |
| O nome do bloqueio no bloqueio dos Serviços de Nuvem (suporte prolongado) está incorreto. | Sem impacto. Solução ainda não disponível. | 
| O nome do endereço IP está incorreto na lâmina do portal Cloud Services (suporte alargado). | Sem impacto. Solução ainda não disponível. | 
| Nome DNS inválido mostrado para endereço IP virtual após a operação de atualização num serviço de nuvem migrada. | Sem impacto. Solução ainda não disponível. | 
| Após uma preparação bem sucedida, não é permitida a ligação de uma nova implementação cloud Services (suporte alargado) como permutável. | Não ligue um novo serviço de nuvem como permutável a um serviço de nuvem preparado. | 
| As mensagens de erro têm de ser atualizadas. | Sem impacto. | 

## <a name="common-migration-errors"></a>Erros comuns de migração
Erros comuns de migração e medidas de mitigação. 

| Mensagem de erro | Detalhes | 
|---|---|
| O tipo de recurso não foi encontrado no espaço de nome para a `Microsoft.Compute` versão api '2020-10-01-preview'. | [Registe a subscrição](in-place-migration-overview.md#setup-access-for-migration) do CloudServices com bandeira para aceder à pré-visualização do público. | 
| O servidor encontrou um erro interno. Recandidutar o pedido. | Relemissar a operação, utilize [o Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ou suporte de contacto. | 
| O servidor encontrou um erro inesperado ao tentar alocar recursos de rede para o serviço na nuvem. Recandidutar o pedido. | Relemissar a operação, utilize [o Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ou suporte de contacto. | 
| O nome de implantação no serviço de nuvem em nuvem deve estar dentro de uma rede virtual a ser migrada. | A implementação não está localizada numa rede virtual. Consulte [este](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) documento para mais detalhes. | 
| A migração do nome de implantação em serviço de nuvem não é suportada porque está em nome da região. Regiões autorizadas: [lista das regiões disponíveis]. | A região ainda não é apoiada para a migração. | 
| O nome de implantação no serviço de nuvem em nuvem não pode ser migrado porque não existem sub-redes associadas ao(s) nome de função. Associe todas as funções a uma sub-rede e, em seguida, retry a migração do serviço em nuvem. | Atualize a implementação do serviço de nuvem (clássico) colocando-o numa sub-rede antes da migração. |  
| O nome de implementação no nome do serviço de nuvem não pode ser migrado porque a implementação requer pelo menos uma funcionalidade que não esteja registada na subscrição no Azure Resource Manager. Registe todas as funcionalidades necessárias para migrar esta implementação. Faltando recursos: [lista de funcionalidades em falta]. | Suporte de contato para obter as bandeiras de recurso registadas. | 
| A implantação não pode ser migrada porque o serviço de nuvem da implantação tem duas faixas horárias ocupadas. A migração de serviços na nuvem é suportada apenas para implementações que são a única implantação no seu serviço na nuvem. Elimine a outra implantação no serviço de nuvem para proceder à migração desta implantação. | Consulte a lista [de cenários não suportados](in-place-migration-overview.md#unsupported-configurations--migration-scenarios) para mais detalhes. | 
| O nome de implementação no nome de serviço de nuvem HostedService está em estado intermédio: estado. Migração não é permitida. | A implementação está a ser criada, eliminada ou atualizada. Aguarde que a operação termine e relemisse. | 
| O nome de implementação em serviço hospedado em nome de serviço cloud reservou IP(s) mas nenhum nome IP reservado. Para resolver este problema, atualize o nome IP reservado ou contacte o balcão de assistência microsoft Azure. | Atualizar a implementação do serviço de nuvem. | 
| O nome de implementação em serviço hospedado em nome de serviço cloud reservou o nome IP(s) reservado ip-name, mas nenhum ponto final no IP reservado. Para resolver este problema, adicione pelo menos um ponto final ao IP reservado. | Adicione o ponto final ao IP reservado. | 
| A migração de Implementação {0} no HostedService {1} está em vias de ser comprometida e não pode ser alterada até que esteja concluída com sucesso.  | Espera ou relemca a operação. | 
| A migração de Implementação {0} no HostedService {1} está em fase de abortar e não pode ser alterada até que esteja concluída com sucesso. | Espera ou relemca a operação. |
| Um ou mais VMs em Implementação {0} no HostedService {1} está a ser submetido a uma operação de atualização. Não pode ser migrado até que a operação anterior termine com sucesso. Tentar depois de algum tempo. | Aguarde que a operação esteja concluída. | 
| A migração não é suportada para implantação {0} em HostedService {1} porque utiliza as seguintes funcionalidades ainda não suportadas para migração: Implementação não-vnet.| A implementação não está localizada numa rede virtual. Consulte [este](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) documento para mais detalhes. | 
| O nome da rede virtual não pode ser nulo ou vazio. | Fornecer nome de rede virtual no corpo de pedido REST | 
| O nome da sub-rede não pode ser nulo ou vazio. | Fornecer o nome da sub-rede no corpo de pedido REST. | 
| DestinationVirtualNetwork deve ser definido para um dos seguintes valores: Padrão, Novo ou Existente. | Fornecer propriedade DestinationVirtualNetwork no corpo de pedido REST. | 
| Opção de destino VNet padrão não implementada. | O valor "predefinido" não é suportado para a propriedade DestinationVirtualNetwork no organismo de pedido REST. | 
| A implantação {0} não pode ser migrada porque o CSPKG não está disponível. | Atualize a implementação e tente novamente. | 
| A sub-rede com ID {0} ' está num local diferente do que a implantação ' ' em serviço hospedado {1} {2} '. A localização da sub-rede é {3} ' e a localização para o serviço hospedado é ' {4} '.  Especifique uma sub-rede no mesmo local que a implantação. | Atualize o serviço de nuvem para ter o serviço de sub-redes e cloud no mesmo local antes da migração. | 
| A migração de Implementação {0} no HostedService {1} está em fase de abortar e não pode ser alterada até que esteja concluída com sucesso. | Aguarde que o abortar complete ou retentou abortar. Utilize [o suporte do Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ou do Contacto de outra forma. | 
| A implantação {0} no HostedService {1} não foi preparada para a migração. | Prepare-se no serviço de nuvem antes de executar a operação de compromisso. | 
| UnknownExceptionInEndExecute: Contract.Assert falhou: rgName é nulo ou vazio: Exceção recebida no EndExecute que não é uma RdfeException. |   Utilize [o suporte&Microsoft Q ou](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) suporte de contacto. | 
| UnknownExceptionInEndExecute: Uma tarefa foi cancelada: Exceção recebida no EndExecute que não é uma RdfeException. | Utilize [o suporte&Microsoft Q ou](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) suporte de contacto. | 
| XrpVirtualNetworkMigrationError: Falha de migração de rede virtual. | Utilize [o suporte&Microsoft Q ou](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) suporte de contacto. | 
| A implementação {0} no HostedService {1}  pertence à Rede {2} Virtual. Migrar a Rede Virtual {2} para migrar este HostedService {1} . | Consulte a [migração da Rede Virtual.](in-place-migration-technical-details.md#virtual-network-migration) | 
| A quota atual de nome de recursos no Gestor de Recursos Azure é insuficiente para completar a migração. A quota atual é {0} , é necessária {1} adicional. Apresentar um pedido de apoio para aumentar a quota e voltar a tentar a migração uma vez que a quota tenha sido aumentada.    | Siga os canais apropriados para solicitar o aumento da quota: <br>[Aumento das quotas para os recursos em rede](../azure-portal/supportability/networking-quota-requests.md) <br>[Aumento das quotas para os recursos computacional](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os requisitos de migração, consulte [detalhes técnicos da migração para a Azure Cloud Services (suporte alargado)](in-place-migration-technical-details.md)
