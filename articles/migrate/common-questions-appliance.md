---
title: Azure Migrate eletrodoméstico FAQ
description: Obtenha respostas a perguntas comuns sobre o aparelho Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 059a1888b529487f2b0d17509370897222a20d83
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563026"
---
# <a name="azure-migrate-appliance-common-questions"></a>Aparelho Azure Migrate: Questões comuns

Este artigo responde a perguntas comuns sobre o aparelho Azure Migrate. Se tiver outras questões, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)
- Perguntas sobre [migração de servidores](common-questions-server-migration.md)
- Obtenha perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>O que é o aparelho Azure Migrate?

O aparelho Azure Migrate é um aparelho leve que o Azure Migrate: A ferramenta de descoberta e avaliação utiliza para descobrir e avaliar servidores físicos ou virtuais a partir de instalações ou de qualquer nuvem. A ferramenta Azure Migrate: Server Migration também utiliza o aparelho para migração sem agentes de servidores no local em execução em ambiente VMware.

Aqui está mais informações sobre o aparelho Azure Migrate:

- O aparelho é implantado no local como um servidor físico ou um servidor virtualizado.
- O aparelho descobre servidores no local e envia continuamente metadados de servidores e dados de desempenho para a Azure Migrate.
- A descoberta do aparelho é sem agente. Nada está instalado em servidores descobertos.

[Saiba mais](migrate-appliance.md) sobre o aparelho.

## <a name="how-can-i-deploy-the-appliance"></a>Como posso colocar o aparelho?

O aparelho pode ser implantado utilizando um par de métodos:

- O aparelho pode ser implantado utilizando um modelo para servidores em execução em ambiente VMware ou Hiper-V[(modelo OVA para VMware](how-to-set-up-appliance-vmware.md) ou [VHD para Hiper-V](how-to-set-up-appliance-hyper-v.md)).
- Se não quiser utilizar um modelo, pode implantar o aparelho para ambiente VMware ou Hiper-V utilizando um [script instalador PowerShell](deploy-appliance-script.md).
- No Governo Azure, deve utilizar o aparelho utilizando um script instalador PowerShell. Consulte os passos de implantação [aqui.](deploy-appliance-script-government.md)
- Para servidores físicos ou virtualizados no local ou em qualquer outra nuvem, utilize sempre o aparelho utilizando um script instalador PowerShell. Consulte os passos de implantação [aqui.](how-to-set-up-appliance-physical.md)

## <a name="how-does-the-appliance-connect-to-azure"></a>Como é que o aparelho se liga ao Azure?

O aparelho pode ligar-se através da internet ou utilizando o Azure ExpressRoute. 

- Certifique-se de que o aparelho pode ligar-se a estes [URLs Azure](./migrate-appliance.md#url-access). 
- Pode utilizar o ExpressRoute com o microsoft a espreitar. O olhar público é preprotecado, e não está disponível para novos circuitos ExpressRoute.
- O olhar privado só não é apoiado.


## <a name="does-appliance-analysis-affect-performance"></a>A análise do aparelho afeta o desempenho?

O Azure Migrate perfis de aparelhos no local continuamente para medir os dados de desempenho. Este perfil não tem quase nenhum impacto de desempenho em servidores com perfil.

## <a name="can-i-harden-the-appliance"></a>Posso endurecer o aparelho?

Quando utilizar o modelo descarregado para criar o aparelho, pode adicionar componentes (antivírus, por exemplo) ao modelo se deixar em vigor as regras de comunicação e firewall que são necessárias para o aparelho Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Que conectividade de rede é necessária?

O aparelho precisa de acesso aos URLs Azure. [Reveja](migrate-appliance.md#url-access) a lista de URL.

## <a name="what-data-does-the-appliance-collect"></a>Que dados o aparelho recolhe?

Consulte os seguintes artigos para obter informações sobre os dados que o aparelho Azure Migrate recolhe nos servidores:

- **Servidores em ambiente VMware**: [Rever os](migrate-appliance.md#collected-data---vmware) dados recolhidos.
- **Servidores em ambiente Hiper-V:** [Rever os](migrate-appliance.md#collected-data---hyper-v) dados recolhidos.
- **Servidores físicos ou virtuais:**[Rever os](migrate-appliance.md#collected-data---physical) dados recolhidos.

## <a name="how-is-data-stored"></a>Como são armazenados os dados?

Os dados recolhidos pelo aparelho Azure Migrate estão armazenados no local Azure onde criou o projeto.

Aqui estão mais informações sobre como os dados são armazenados:

- Os dados recolhidos são armazenados de forma segura no CosmosDB numa subscrição da Microsoft. Os dados são eliminados quando elimina o projeto. O armazenamento é manuseado pela Azure Migrate. Não é possível escolher especificamente uma conta de armazenamento para os dados recolhidos.
- Se utilizar [a visualização de dependência,](concepts-dependency-visualization.md)os dados recolhidos são armazenados num espaço de trabalho Azure Log Analytics criado na sua subscrição Azure. Os dados são eliminados quando elimina o espaço de trabalho Do Log Analytics na sua subscrição.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Quantos dados são carregados durante o perfil contínuo?

O volume de dados enviados para a Azure Migrate depende de vários parâmetros. Como exemplo, um projeto que tem 10 servidores (cada um com um disco e um NIC) envia aproximadamente 50 MB de dados por dia. Este valor é aproximado; o valor real varia dependendo do número de pontos de dados para os discos e NICs. Se o número de servidores, discos ou NICs aumentar, o aumento dos dados enviados não é linear.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Os dados são encriptados em repouso e em trânsito?

Sim, para ambos:

- Os metadados são enviados de forma segura para o serviço Azure Migrate através da internet através de HTTPS.
- Os metadados são armazenados numa base de [dados Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento Azure Blob](../storage/common/storage-service-encryption.md) numa subscrição da Microsoft. Os metadados são encriptados em repouso para armazenamento.
- Os dados para análise de dependência também são encriptados em trânsito (por HTTPS seguro). Está guardado num espaço de trabalho do Log Analytics na sua subscrição. Os dados são encriptados em repouso para análise de dependência.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Como é que o aparelho se liga ao servidor vCenter?

Estes passos descrevem como o aparelho se conecta ao VMware vCenter Server:

1. O aparelho liga-se ao servidor vCenter (porta 443) utilizando as credenciais fornecidas quando configurar o aparelho.
2. O aparelho utiliza o VMware PowerCLI para consultar o vCenter Server para recolher metadados sobre os servidores que são geridos pelo vCenter Server.
3. O aparelho recolhe dados de configuração sobre servidores (núcleos, memória, discos, NICs) e o histórico de desempenho de cada servidor durante o mês passado.
4. Os metadados recolhidos são enviados para o Azure Migrate: Ferramenta de descoberta e avaliação (através da internet via HTTPS) para avaliação.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>O aparelho Azure Migrate pode ligar-se a vários servidores vCenter?

N.º Há um mapeamento de um para um entre um [aparelho Azure Migrate](migrate-appliance.md) e vCenter Server. Para descobrir servidores em várias instâncias do VCenter Server, tem de implementar vários aparelhos.

## <a name="can-a-project-have-multiple-appliances"></a>Um projeto pode ter vários aparelhos?

Um projeto pode ter vários aparelhos registados. No entanto, um aparelho só pode ser registado com um projeto.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>O aparelho Azure Migrate/aparelho de replicação pode ligar-se ao mesmo vCenter?

Sim. Pode adicionar tanto o aparelho Azure Migrate (utilizado para avaliação e migração de VMware sem agente) como o aparelho de replicação (utilizado para a migração baseada em agentes de servidores em execução em VMware) ao mesmo servidor vCenter. Mas certifique-se de que não está a configurar ambos os aparelhos no mesmo servidor e que não está suportado atualmente.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Quantos servidores posso descobrir com um aparelho?

Pode descobrir até 10.000 servidores em ambiente VMware, até 5.000 servidores em ambiente Hiper-V e até 1000 servidores físicos com um único aparelho. Se tiver mais servidores no seu ambiente no local, leia sobre [o dimensionamento de uma avaliação de Hiper-V,](scale-hyper-v-assessment.md) [escalonando uma avaliação de VMware](scale-vmware-assessment.md)e [escalando uma avaliação do servidor físico](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Posso apagar um aparelho?

Atualmente, a eliminação de um aparelho do projeto não é suportada.

A única forma de eliminar o aparelho é eliminar o grupo de recursos que contém o projeto associado ao aparelho.

No entanto, a eliminação do grupo de recursos também elimina outros aparelhos registados, o inventário descoberto, avaliações e todos os outros componentes Azure no grupo de recursos que estão associados ao projeto.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Posso utilizar o aparelho com uma subscrição ou projeto diferente?

Para utilizar o aparelho com uma subscrição ou projeto diferente, seria necessário reconfigurar o aparelho existente executando o script do instalador PowerShell para o cenário específico (VMware/Hyper-V/Physical) no aparelho. O script limpará os componentes e configurações do aparelho existentes para implantar um aparelho fresco. Certifique-se de que limpa a cache do navegador antes de começar a utilizar o gestor de configuração do aparelho recém-implantado.

Além disso, não é possível reutilizar uma chave de projeto existente num aparelho reconfigurado. Certifique-se de que gera uma nova chave a partir da subscrição/projeto pretendido para completar o registo do aparelho.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Posso montar o aparelho num Azure VM?

N.º Atualmente, esta opção não é suportada.

## <a name="can-i-discover-on-an-esxi-host"></a>Posso descobrir num hospedeiro ESXi?

N.º Para descobrir servidores em ambiente VMware, tem de ter o vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Como atualizei o aparelho?

Por predefinição, o aparelho e os seus agentes instalados são atualizados automaticamente. O aparelho verifica as atualizações a cada 24 horas. As atualizações que falham são novamente experimentadas. 

Apenas o aparelho e os agentes do aparelho são atualizados através destas atualizações automáticas. O sistema operativo não é atualizado pelas atualizações automáticas da Azure Migrate. Utilize atualizações do Windows para manter o sistema operativo atualizado.

## <a name="can-i-check-agent-health"></a>Posso verificar a saúde do agente?

Sim. No portal, vá à página **de saúde** do Agente para o Azure Migrate: Discovery and assessment or Azure Migrate: Server Migration tool. Lá, pode verificar o estado de ligação entre a Azure e os agentes de descoberta e avaliação do aparelho.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>Posso adicionar várias credenciais de servidor no aparelho VMware?

Sim, agora suportamos múltiplas credenciais de servidor para realizar o inventário de software (descoberta de aplicações instaladas), análise de dependência de agentes e descoberta de instâncias e bases de dados do SQL Server. [Saiba mais](tutorial-discover-vmware.md#provide-server-credentials) sobre como fornecer credenciais no gestor de configuração do aparelho.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Que tipo de credenciais de servidor posso adicionar no aparelho VMware?
Pode fornecer credenciais de autenticação de domínio/ Windows (não domínio)/ Linux (não domínio)/ SQL Server no gestor de configuração do aparelho. [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as lidamos.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Que tipo de propriedades de conexão SQL Server são suportadas pela Azure Migrate para a descoberta do SQL?
A Azure Migrate encriptará a comunicação entre o aparelho Azure Migrate e as instâncias do SQL Server de origem (com a propriedade de ligação encriptada definida para TRUE). Estas ligações são encriptadas com [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (definido para TRUE); a camada de transporte utilizará o SSL para encriptar o canal e contornar a cadeia de certificados para validar a confiança. O servidor do aparelho deve ser criado para [confiar na autoridade principal do certificado](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Se não tiver sido fornectado nenhum certificado no servidor quando este começar, o SQL Server gera um certificado auto-assinado que é utilizado para encriptar pacotes de login. [Saiba mais](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).


## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).