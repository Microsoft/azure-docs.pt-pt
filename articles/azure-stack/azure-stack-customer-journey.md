---
title: Instruções de integração de datacenter do Azure Stack | Documentos da Microsoft
description: Saiba o que esperar para uma implantação bem-sucedida no local do Azure Stack no seu datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: ad58b8f1034a0e2818d3aca53c023736da9370cb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119926"
---
# <a name="azure-stack-datacenter-integration"></a>Integração de datacenter do Azure Stack

Este artigo descreve a experiência do cliente do Azure Stack ponto a ponto na compra de um sistema integrado por meio de uma implementação no local com êxito por um fornecedor de solução. Utilize estas informações para facilitar a sua jornada de e para ajudar a definir expectativas para, um cliente do Azure Stack.

Enquanto cliente do Azure Stack, deve prever as seguintes fases:

|     |Fase de planejamento|Processo de encomenda|Pré-implementação|Processo de fábrica|Entrega de hardware|Implementação no local|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Interaja com os parceiros para fornecer suporte de pré-vendas.|Prepare o licenciamento de software e contratos conforme necessário.|Fornecem as ferramentas necessárias para recolher os requisitos de integração do Centro de dados e a documentação para o cliente.|Fornece as compilações de linha de base e a ferramenta mais recentes atualizações de cadeia a uma cadência mensal.|N/A|Engenheiros de suporte da Microsoft, ajudá-lo com possíveis problemas de implantação.|
|**Parceiro**|Recomendamos as opções de solução com base nos requisitos de cliente.<br><br>Propor uma prova de conceito (POC), se necessário.<br><br>Estabelece uma relação de negócios.<br><br>Escolher o nível de suporte.|Prepare necessários contratos com o cliente.<br><br>Crie a ordem de compra do cliente.<br><br>Decidir qual a linha cronológica de entrega.<br><br>Ligar ao cliente com a Microsoft se necessário.|Fornece ao cliente com o treinamento necessário para assegurar a compreensão de todos os pré-requisitos de implementação e datacenter opções de integração.<br><br>Ajudar o cliente com a validação de dados recolhidos para garantir a integridade e a precisão.|Aplicam-se a última compilação de linha de base validados.<br><br>Aplicam-se o Microsoft deployment toolkit necessário.|Envie o hardware para o site do cliente.|Implementação manipulada por um engenheiro de no local.<br><br>Bastidor e pilha.<br><br>Implementação do anfitrião (HLH) de ciclo de vida de hardware.<br><br>Implementação de pilha do Azure.<br><br>Mão desativado para o cliente.|
|**Cliente**|Descrever casos de utilização prevista e especificar os requisitos.|Determine o modelo de faturação para utilizar, rever e aprovar os contratos.|Preencher a planilha de implementação e certifique-se de que todos os pré-requisitos de implementação são cumpridos e pronto para implantação.|N/A|Preparar o Centro de dados, garantindo a energia tudo necessários e resfriamento, conectividade de limite e outro requisito de integração do Centro de dados necessários estão em vigor.|Estar disponível durante a implementação para fornecer credenciais de subscrição e suportar se existem perguntas sobre os dados fornecidos.|
| | | | | | | |


## <a name="planning-phase"></a>Fase de planejamento
A fase de planeamento é quando a Microsoft ou o parceiro de solução do Azure Stack, irá trabalhar para avaliar e compreender suas necessidades para determinar se o Azure Stack é a solução certa para:

Elas ajudam a decidir o seguinte:

-   É o Azure Stack a solução certa para a sua organização?

-   O que é solução de tamanho vai precisar?

-   Que tipo de modelo de faturação e licenciamento irá funcionar para a sua organização?

-   Quais são os requisitos de refrigeração e power?

Para se certificar de que a solução de hardware será melhor se adequam às suas necessidades, o [Planeador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) podem ser utilizados para ajudar na compra prévia de planejamento para determinar a capacidade adequada e a configuração do hardware do Azure Stack solução.

É a folha de cálculo *não* se destina a ser utilizado como um substituto para sua própria investigação e análise de soluções de hardware que melhor se adequar às suas necessidades. Quando planear uma implementação do Azure Stack, também deverá rever os [considerações de integração do Centro de dados gerais](azure-stack-datacenter-integration.md) para o Azure Stack de sistemas integrados.

## <a name="order-process-phase"></a>Fase do processo de encomenda
Nesta fase, muitas das suas perguntas com respeito a viabilidade seriam foram respondidas. Agora que já está pronto para consolidar para comprar o Azure Stack e, depois de iniciar todos os contratos necessários e ordens de compra, será solicitado para fornecer os dados de requisitos de integração para o seu fornecedor de soluções.

## <a name="pre-deployment-phase"></a>Fase de pré-implementação
Durante esta fase, terá de decidir como pretende integrar o Azure Stack no seu datacenter. Para facilitar esse processo, Microsoft, em colaboração com os fornecedores de soluções, criar um modelo de requisitos para o ajudar a recolher as informações necessárias para planejar uma implantação de sistema integrado no seu ambiente.

O [considerações de integração do Centro de dados gerais](azure-stack-datacenter-integration.md) artigo fornece informações que o ajuda a concluir o modelo, conhecido como a folha de cálculo de implementação. 

> [!IMPORTANT]
> Durante esta fase é importante que todas as informações de pré-requisitos são investigadas e decidir antes da ordenação a solução. Lembre-se de que este passo é demorado e exige a coordenação e a recolha de dados de várias disciplinas dentro da sua organização. Informações incorretas ou incompletas podem resultar numa implantação mais tempo. 

Na fase de pré-implementação terá de decidir o seguinte:

- **O Azure Stack modelo e a identidade do fornecedor de ligação**. Pode optar por implementar o Azure Stack tanto [conectado à internet (e para o Azure) ou desconectado](azure-stack-connection-models.md). Para tirar o máximo proveito da pilha do Azure, incluindo cenários híbridos, iria querer implementar ligado ao Azure. A escolha de serviços de Federação do Active Directory (AD FS) ou Azure Active Directory (Azure AD) é uma decisão única que é necessário efetuar no momento da implementação. **Não pode alterar isto mais tarde voltar a implementar todo o sistema**.

- **Modelo de licenciamento**. Opções do modelo de licenciamento para a sua escolha dependem do tipo de implementação que terá. Sua escolha de fornecedor de identidade não tem nenhuma relevância em máquinas de virtuais de inquilino ou o sistema de identidade e contas que utilizam.
    - Os clientes que estão num [desligado implementação](azure-stack-disconnected-deployment.md) tem apenas uma opção: faturação com base na capacidade.

    - Os clientes que estão num [ligado implementação](azure-stack-connected-deployment.md) pode escolher entre baseado em capacidades de faturação e pagamento-como-utiliza. Faturação com base na capacidade requer uma subscrição do Azure Enterprise Agreement (EA) para o registo. Isto é necessário para o registo, que fornece a disponibilidade de itens no Marketplace através de uma subscrição do Azure.

- **Integração de rede**. [Integração de rede](azure-stack-network.md) é crucial para implantação, operação e gerenciamento de sistemas do Azure Stack. Existem várias considerações que vão para garantir que a solução do Azure Stack é resiliente e tem uma infraestrutura física elevada disponibilidade para suportar as suas operações.

- **Integração da firewall**. É recomendável que [utilizar uma firewall](azure-stack-firewall.md) para ajudar a proteger do Azure Stack. Firewalls podem ajudar a impedir ataques DDOS, detecção de intrusão e inspeção de conteúdo. No entanto, é importante observar que ele pode se tornar um gargalo de serviços de armazenamento do Azure.


- **Requisitos de certificado**. É fundamental que todos os [certificados necessários](azure-stack-pki-certs.md) estão disponíveis *anterior* até um engenheiro de no local a chegar ao seu datacenter para a implementação.

Quando todas as informações de pré-requisitos são recolhidas por meio da folha de cálculo de implementação, o fornecedor de soluções para iniciar o processo de fábrica com base nos dados recolhidos para garantir uma integração com êxito do Azure Stack no seu datacenter.

## <a name="hardware-delivery-phase"></a>Fase de entrega de hardware
O fornecedor de solução irá trabalhar no agendamento quando a solução vai deparar-se para suas instalações. Uma vez recebido e colocadas em vigor, precisará de agendar hora com o fornecedor de soluções para ter um engenheiro são fornecidos no local para executar a implantação do Azure Stack.

É **crucial** que todos os dados de pré-requisito está bloqueado e disponível *antes do no local, engenheiro chega para implantar a solução*.

-   Todos os certificados têm de ser adquiridos e pronto.

-   Nome da região deve ser decidir.

-   Todos os parâmetros de integração de rede são finalizados e coincide com o que tenha partilhado com o seu fornecedor de soluções.

> [!TIP]
> Se essas informações tiverem sido alterados, certifique-se comunicar a alteração com o fornecedor de solução antes de agendar a implantação real.

## <a name="onsite-deployment-phase"></a>Fase de implementação no local
Para implementar o Azure Stack, terá de estar presente para iniciar a implementação de um engenheiro de no local através do seu fornecedor de solução de hardware. Para garantir uma implementação com êxito, certifique-se de que todas as informações fornecidas por meio da folha de cálculo de implementação não foi alterado. 

Segue-se que deve esperar o engenheiro de no local durante a experiência de implementação:

- Verifique a todos os conectividade de cablagem e o limite para garantir que a solução está corretamente estruturada e cumpre os seus requisitos.
- Configure a solução HLH (anfitrião de ciclo de vida de Hardware), se estiver presente.
- Verifique se que as definições de todos os BMC, BIOS e de rede estão corretas.
- Certifique-se o firmware de todos os componentes na versão mais recente aprovada pela solução.
- Inicie a implementação.

> [!NOTE]
> A conclusão, um procedimento de implementação, o engenheiro de no local pode demorar aproximadamente uma semana de negócios.

## <a name="post-deployment-phase"></a>Fase de pós-implementação
Antes da solução é transferida para o cliente na fase de pós-integração, vários passos devem ser executados pelo parceiro. Nesta fase, a validação é importante certificar-se de que a implementação do sistema e de desempenho corretamente. 

Ações que devem ser realizadas pelo parceiro OEM são:

- [Executar teste azurestack](azure-stack-diagnostic-test.md#run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix)

- [Registo com o Azure](azure-stack-registration.md)

- [Distribuição de mercado](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

- Ficheiros de configuração do comutador e a configuração de HLH cópia de segurança

- Remover DVM

- Preparar um resumo de cliente para a implementação

- [Verificar atualizações para tornar-se de que o software de solução é atualizado para a versão mais recente](./azure-stack-updates.md)

Há várias etapas que são necessárias ou opcionais, consoante o tipo de instalação.

- Se a implementação foi concluída com [do AD FS](azure-stack-integrate-identity.md), em seguida, o carimbo de data / tem de ser integrado com o cliente do Azure Stack do próprio do AD FS.

  > [!NOTE]
  > Este passo é da responsabilidade do cliente, embora o parceiro pode optar por oferecer serviços para fazer isso.

- Integração com um sistema de monitorização existente do respetivo parceiro.

  -   [Integração do System Center Operations Manager](azure-stack-integrate-monitor.md) também suporta as capacidades de gestão de frotas.

  -   [Integração da Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>Linha cronológica geral

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Suporte
O Azure Stack permite uma consistentes do Azure, experiência de suporte integrado que abrange o ciclo de vida de todo o sistema. Para suportar totalmente integrado do Azure Stack sistemas, os clientes têm de dois contratos de suporte; um junto da Microsoft (ou o fornecedor de soluções Cloud) para serviços do Azure e outro com o fornecedor de hardware para o suporte do sistema. A experiência de suporte integrado fornece coordenado Escalamento e resolução, para que os clientes obter uma experiência de suporte consistente, independentemente de quem eles chamam pela primeira vez. Para os clientes que já tem o Premier, do Azure - Standard / o suporte ProDirect ou parceiro com a Microsoft, o suporte de software do Azure Stack está incluído.

A experiência de suporte integrada faz uso de um câmbio de caso de mecanismo para a transferência de bidirecional de incidentes de suporte e atualizações casos entre a Microsoft e os parceiros de hardware. Microsoft Azure Stack irá seguir a [política de ciclo de vida moderno](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [considerações de integração do Centro de dados gerais](azure-stack-datacenter-integration.md).
