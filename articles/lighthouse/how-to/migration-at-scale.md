---
title: Gerir projetos da Azure Migrate em escala
description: Saiba como utilizar eficazmente a Azure Migrate em recursos de clientes delegados.
ms.date: 01/29/2021
ms.topic: how-to
ms.openlocfilehash: ed2fc8ae1a3b87a3b2501c3a390fe7a1822003e4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393586"
---
# <a name="manage-azure-migrate-projects-at-scale-with-azure-lighthouse"></a>Gerir projetos da Azure Migrate em escala com o Farol de Azure

Este tópico fornece uma visão geral de como [o Farol Azure](../overview.md) pode ajudá-lo a usar [Azure Migrate](../../migrate/migrate-services-overview.md) de uma forma escalável através de vários inquilinos do Azure Ative Directory (Azure AD).

O Azure Lighthouse permite que os prestadores de serviços realizem operações em escala em vários inquilinos ao mesmo tempo, tornando as tarefas de gestão mais eficientes.

A Azure Migrate fornece um hub centralizado para avaliar e migrar para Azure nos servidores, infraestruturas, aplicações e dados. Normalmente, os parceiros que realizam avaliações e migração em escala para vários clientes devem aceder individualmente a cada subscrição do cliente utilizando o [modelo de subscrição CSP (Cloud Solution Provider)](/partner-center/customers-revoke-admin-privileges) ou [criando um utilizador convidado no cliente inquilino.](../../active-directory/external-identities/what-is-b2b.md)

A integração do Azure Lighthouse com a Azure Migrate permite que os prestadores de serviços descubram, avaliem e migram cargas de trabalho para diferentes clientes em escala, permitindo ao mesmo tempo que os clientes tenham total visibilidade e controlo dos seus ambientes. Através da gestão de recursos delegada da Azure, os prestadores de serviços têm uma visão única de todos os projetos da Azure Migrate que gerem através de vários inquilinos de clientes.

> [!NOTE]
> Através do Farol Azure, os parceiros podem realizar descobertas, avaliação e migração para VMware VMs no local, VMs Hiper-V, servidores físicos e instâncias AWS/GCP. Existem duas opções para [a migração VMware VM.](../../migrate/server-migrate-overview.md) Atualmente, apenas o método de migração baseado em agentes pode ser utilizado quando se trabalha num projeto de migração numa subscrição de clientes delegados; a migração utilizando a replicação sem agente não é suportada atualmente através do acesso delegado ao âmbito do cliente.

> [!TIP]
> Embora nos refiramos a prestadores de serviços e clientes neste tópico, esta orientação também se aplica às [empresas que utilizam o Farol Azure para gerir vários inquilinos.](../concepts/enterprise.md)

Dependendo do seu cenário, poderá desejar criar o Azure Migrate no inquilino do cliente ou no seu inquilino gerente. Reveja as considerações abaixo e determine qual o modelo que melhor se adequa às necessidades de migração do seu cliente.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Criar um projeto Azure Migrate no inquilino do cliente

Uma opção ao utilizar o Farol Azure é criar o projeto Azure Migrate no inquilino do cliente. Os utilizadores do inquilino gerente podem então selecionar a subscrição do cliente ao criar um projeto de migração. A partir do inquilino gerente, o prestador de serviços pode realizar as operações de migração necessárias. Isto pode incluir a implantação do aparelho Azure Migrate para descobrir as cargas de trabalho, avaliar as cargas de trabalho agrupando VMs e calculando os custos relacionados com a nuvem, revendo a prontidão em VM e realizando a migração.

Neste cenário, não serão criados e armazenados recursos no arrendatário gerente, mesmo que as etapas de descoberta e avaliação possam ser iniciadas e executadas a partir desse inquilino. Todos os recursos, tais como projetos de migração, relatórios de avaliação para cargas de trabalho on-prem e recursos migrados no destino-alvo, serão implantados na subscrição do cliente. No entanto, o prestador de serviços pode aceder a todos os projetos do cliente a partir da sua própria experiência de inquilino e portal.

Esta abordagem minimiza a mudança de contexto para prestadores de serviços que trabalham em vários clientes, ao mesmo tempo que permite que os clientes mantenham todos os seus recursos nos seus próprios inquilinos.

O fluxo de trabalho para este modelo será semelhante ao seguinte:

1. O cliente está [a bordo do Farol de Azure.](onboard-customer.md) O papel incorporado do Contribuinte é necessário para a identidade que será usada com Azure Migrate. Consulte o modelo de amostra de [gestão de recursos-azmigrate delegado](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) para um exemplo utilizando esta função.
1. O utilizador designado assina no inquilino gerente no portal Azure, e depois vai para Azure Migrate. Este utilizador [cria um projeto Azure Migrate,](../../migrate/create-manage-projects.md)selecionando a subscrição de clientes delegados apropriado.
1. Em seguida, o utilizador [executa passos para a descoberta e avaliação](../../migrate/tutorial-discover-vmware.md).

   Para VMware VMs, antes de configurar o aparelho, pode limitar a descoberta a centros de dados vCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou VMs individuais. Para definir o âmbito, atribua permissões na conta que o aparelho utiliza para aceder ao servidor vCenter. Isto é útil se os VM de vários clientes estiverem hospedados no hipervisor. Não se pode limitar o alcance de descoberta do Hiper-V.

    > [!NOTE]
    > Pode descobrir e avaliar máquinas virtuais VMware para migração utilizando o Azure Migrate através do acesso delegado fornecido pelo Farol de Azure. Para a migração de máquinas virtuais VMware, apenas o método baseado em agente é atualmente suportado quando trabalha num projeto de migração numa subscrição de clientes delegados.

1. Quando a subscrição do cliente-alvo estiver pronta, proceda com a migração através do acesso concedido pelo Farol Azure. O projeto de migração que contenha resultados de avaliação e recursos migrados será criado no cliente inquilino no âmbito da subscrição-alvo.

> [!TIP]
> Antes da migração, será necessário implantar uma zona de desembarque para a disponibilização dos recursos de infraestrutura da fundação e preparar a subscrição para a qual as máquinas virtuais serão migradas. Para aceder ou criar alguns recursos nesta zona de aterragem, pode ser necessária a função incorporada do Proprietário, que não é suportada atualmente no Farol de Azure. Para tais cenários, o cliente poderá ter de fornecer função de acesso ao hóspede ou delegar o acesso a administrador através do modelo de subscrição CSP. Para obter uma abordagem para criar zonas de aterragem multi-arrendatários, consulte a [solução de demonstração multi-inquilino-Zonas de Aterragem](https://github.com/Azure/Multi-tenant-Landing-Zones) no GitHub.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>Criar um projeto Azure Migrate no inquilino gerente

Neste cenário, as operações relacionadas com a migração, como a descoberta e a avaliação, são ainda realizadas pelos utilizadores do inquilino gerente. No entanto, o projeto de migração e todos os recursos relevantes residirão no inquilino parceiro, e o cliente não terá visibilidade direta no projeto (embora as avaliações possam ser partilhadas com os clientes, se desejar). O destino de migração para cada cliente será a subscrição do cliente.

Esta abordagem permite que os prestadores de serviços iniciem rapidamente projetos de descoberta e avaliação de migração, resumindo os passos iniciais das assinaturas de clientes e inquilinos.

O fluxo de trabalho para este modelo será semelhante ao seguinte:

1. O cliente está [a bordo do Farol de Azure.](onboard-customer.md) O papel incorporado do Contribuinte é necessário para a identidade que será usada com Azure Migrate. Consulte o modelo de amostra de [gestão de recursos-azmigrate delegado](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) para um exemplo utilizando esta função.
1. O utilizador designado assina no inquilino gerente no portal Azure, e depois vai para Azure Migrate. Este utilizador [cria um projeto Azure Migrate](../../migrate/create-manage-projects.md) numa subscrição pertencente ao inquilino gerente.
1. Em seguida, o utilizador [executa passos para a descoberta e avaliação](../../migrate/tutorial-discover-vmware.md). Os VMs no local serão descobertos e avaliados no âmbito do projeto de migração criado no inquilino gerente, migrando depois de lá.

   Se estiver a gerir vários clientes no mesmo anfitrião Hyper-V, pode descobrir todas as cargas de trabalho ao mesmo tempo. Os VMs específicos do cliente podem ser selecionados no mesmo grupo, então uma avaliação pode ser criada, e a migração pode ser realizada selecionando a subscrição do cliente apropriado como destino alvo. Não há necessidade de limitar o âmbito de descoberta, e você pode manter uma visão geral completa de todas as cargas de trabalho do cliente em um projeto de migração.

1. Quando estiver pronto, proceda com a migração selecionando a subscrição do cliente delegado como destino-alvo para replicar e migrar as cargas de trabalho. Os recursos recém-criados existirão na subscrição do cliente, enquanto os dados de avaliação e recursos relativos ao projeto de migração permanecerão no inquilino gestor.

NOTA: Tem de modificar o ficheiro de parâmetros para refletir o seu ambiente antes de se implantar https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>Reconhecimento de parceiros para migrações de clientes

Como membro da [Microsoft Partner Network,](https://partner.microsoft.com)pode ligar o ID do seu parceiro às credenciais utilizadas para gerir os recursos do cliente delegado. Isto permite à Microsoft atribuir influência e a Azure consumiu receitas à sua organização com base nas tarefas que executa para os clientes, incluindo projetos de migração.

Para obter mais informações, veja [Ligar o seu ID de parceiro para controlar o impacto nos recursos delegados](partner-earned-credit.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Azure Migrate.](../../migrate/migrate-services-overview.md)
- Conheça outras [experiências de gestão de inquilinos cruzadas apoiadas](../concepts/cross-tenant-management-experience.md) pelo Farol de Azure.
