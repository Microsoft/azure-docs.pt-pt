---
title: Ferramenta de migração suportada pela plataforma.
description: Mergulho técnico profundo na migração de recursos suportado pela plataforma do modelo de implementação clássico para Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: tagore
ms.openlocfilehash: bc12d626d8a331981cbbad015b376b826c617209
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735137"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Technical deep dive on platform-supported migration from classic to Azure Resource Manager (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)

> [!IMPORTANT]
> Hoje, cerca de 90% dos VMs da IaaS estão a usar [o Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) A partir de 28 de fevereiro de 2020, os VM clássicos foram depreciados e serão totalmente retirados a 1 de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre esta depreciação e [como isso o afeta.](./classic-vm-deprecation.md#how-does-this-affect-me)

Vamos mergulhar profundamente na migração do modelo clássico de implementação do Azure para o modelo de implementação do Azure Resource Manager. Olhamos para os recursos a um nível de recursos e recursos para ajudá-lo a entender como a plataforma Azure migra recursos entre os dois modelos de implementação. Para mais informações, leia o artigo de anúncio de serviço: [Migração suportada pela plataforma de recursos IaaS do clássico para o Azure Resource Manager.](migration-classic-resource-manager-overview.md)


## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrar recursos iaaS do modelo de implementação clássico para Azure Resource Manager
Em primeiro lugar, é importante entender a diferença entre as operações de data-plano e de gestão-avião na infraestrutura como recursos de serviço (IaaS).

* *O plano de gestão/controlo* descreve as chamadas que entram no plano de gestão/controlo ou na API para modificar recursos. Por exemplo, operações como criar uma VM, reiniciar uma VM e atualizar uma rede virtual com uma nova sub-rede gerem os recursos em execução. Não afetam diretamente a ligação aos VMs.
* O plano de *dados* (aplicação) descreve o tempo de funcionamento da própria aplicação, e envolve interação com casos que não passam pela API Azure. Por exemplo, aceder ao seu website, ou retirar dados de uma instância do SQL Server em execução ou de um servidor MongoDB, são interações de plano de dados ou aplicações. Outros exemplos incluem copiar uma bolha de uma conta de armazenamento e aceder a um endereço IP público para utilizar o Protocolo de Ambiente de Trabalho Remoto (RDP) ou Secure Shell (SSH) na máquina virtual. Estas operações mantêm a aplicação em execução em processos, redes e armazenamento.

O plano de dados é o mesmo entre o modelo de implementação clássico e as pilhas de Gestor de Recursos. A diferença é que durante o processo de migração, a Microsoft traduz a representação dos recursos do modelo de implementação clássico para o da pilha de Gestor de Recursos. Como resultado, precisa de utilizar novas ferramentas, APIs e SDKs para gerir os seus recursos na pilha de Gestor de Recursos.

![Diagrama que mostra a diferença entre plano de gestão/controlo e plano de dados](./media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Em alguns cenários de migração, a plataforma Azure para, desaloca e reinicia as suas máquinas virtuais. Isto causa um breve tempo de inatividade do avião de dados.
>

## <a name="the-migration-experience"></a>A experiência de migração
Antes de iniciar a migração:

* Confirme que os recursos que quer migrar não utilizam funcionalidades ou configurações não suportadas. Normalmente, a plataforma deteta estes problemas e gera um erro.
* Se tiver VMs que não estejam numa rede virtual, são parados e translocados como parte da operação de preparação. Se não quiser perder o endereço IP público, considere reservar o endereço IP antes de desencadear a operação de preparação. Se os VMs estiverem numa rede virtual, não são parados e translocados.
* Planeie a migração para ocorrer fora do horário de expediente, de modo a poder dar resposta a falhas inesperadas que possam surgir.
* Transfira a configuração atual das suas VMs com o PowerShell, os comandos da interface de linha de comandos (CLI) ou as APIs REST, de modo a facilitar a validação após a fase de preparação estar concluída.
* Atualize os scripts de automação e operacionalização para lidar com o modelo de implementação do Gestor de Recursos, antes de iniciar a migração. Opcionalmente, pode efetuar operações GET quando os recursos estão no estado pronto.
* Avalie as políticas de controlo de acesso baseado em funções Azure (Azure RBAC) que estão configuradas nos recursos iaaS no modelo de implementação clássico, e planeie para depois da migração estar completa.

O fluxo de trabalho da migração é o seguinte:

![Diagrama que mostra o fluxo de trabalho da migração](./media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> As operações descritas nas seguintes secções são todas idempotentes. Se tiver outro problema que não seja uma funcionalidade não suportada ou um erro de configuração, recava o teste de preparação, abortar ou cometer o funcionamento. Azure tenta a ação de novo.
>
>

### <a name="validate"></a>Validação
A operação de validação é o primeiro passo do processo de migração. O objetivo deste passo é analisar o estado dos recursos que pretende migrar no modelo clássico de implantação. A operação avalia se os recursos são capazes de migrar (sucesso ou fracasso).

Selecione a rede virtual ou um serviço de nuvem (se não estiver numa rede virtual) que pretende validar para migração. Se o recurso não for capaz de migrar, a Azure enumera as razões.

#### <a name="checks-not-done-in-the-validate-operation"></a>Verificações não efetuadas na operação validada

A operação validada apenas analisa o estado dos recursos no modelo clássico de implantação. Pode verificar todas as falhas e cenários não suportados devido a várias configurações no modelo de implementação clássico. Não é possível verificar todas as questões que a stack do Gestor de Recursos Azure pode impor aos recursos durante a migração. Estas questões só são verificadas quando os recursos passam por transformação no próximo passo da migração (a operação de preparação). A tabela que se segue lista todas as questões não verificadas na operação validada:


|Verificações de rede não na operação validada|
|-|
|Uma rede virtual com gateways ER e VPN.|
|Uma ligação virtual de gateway de rede num estado desligado.|
|Todos os circuitos ER são pré-migrados para a pilha Azure Resource Manager.|
|Azure Resource Manager verifica os recursos em rede. Por exemplo: IP público estático, IPs público dinâmico, balanceador de carga, grupos de segurança de rede, tabelas de rotas e interfaces de rede. |
| Todas as regras do balançador de carga são válidas em toda a implementação e na rede virtual. |
| IPs privados contraditórios entre VMs stop-deallocados na mesma rede virtual. |

### <a name="prepare"></a>Preparação
A operação de preparação é o segundo passo do processo de migração. O objetivo deste passo é simular a transformação dos recursos iaaS do modelo de implantação clássico para recursos de Gestor de Recursos. Além disso, a operação de preparação apresenta este lado a lado para visualizar.

> [!NOTE] 
> Os seus recursos no modelo clássico de implantação não são modificados durante este passo. É um passo seguro para correr se estiveres a tentar migrar. 

Selecione a rede virtual ou o serviço de nuvem (se não for uma rede virtual) que pretende preparar para a migração.

* Se o recurso não for capaz de migrar, o Azure para o processo de migração e enumera a razão pela qual a operação de preparação falhou.
* Se o recurso for capaz de migrar, a Azure bloqueia as operações de gestão-avião para os recursos em migração. Por exemplo, não pode adicionar um disco de dados a uma VM em migração.

A azure inicia então a migração de metadados do modelo de implementação clássico para o Gestor de Recursos para os recursos migratórios.

Após a conclusão da operação de preparação, tem a opção de visualizar os recursos tanto no modelo clássico de implementação como no Gestor de Recursos. A plataforma Azure cria um nome de grupo de recursos, com o padrão `cloud-service-name>-Migrated`, para cada serviço cloud no modelo de implementação clássica.

> [!NOTE]
> Não é possível selecionar o nome de um grupo de recursos criado para recursos migrados (isto é, "-Migrado"). No entanto, após a conclusão da migração, pode utilizar a funcionalidade de movimento do Azure Resource Manager para mover recursos para qualquer grupo de recursos que pretenda. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

As duas imagens seguintes mostram o resultado após uma operação de preparação bem sucedida. O primeiro mostra um grupo de recursos que contém o serviço de nuvem original. O segundo mostra o novo grupo de recursos "-Migrado" que contém os recursos equivalentes do Azure Resource Manager.

![Screenshot que mostra serviço de nuvem original](./media/migration-classic-resource-manager/portal-classic.png)

![Screenshot que mostra recursos do Gestor de Recursos Azure na operação de preparação](./media/migration-classic-resource-manager/portal-arm.png)

Aqui está um olhar dos bastidores para os seus recursos após a conclusão da fase de preparação. Note que o recurso no plano de dados é o mesmo. Está representado tanto no plano de gestão (modelo de implantação clássico) como no plano de controlo (Gestor de Recursos).

![Diagrama da fase de preparação](./media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Os VMs que não estão numa rede virtual no modelo de implementação clássico são parados e translocados nesta fase de migração.
>

### <a name="check-manual-or-scripted"></a>Verificação (manual ou com script)
Na etapa de verificação, tem a opção de utilizar a configuração que descarregou anteriormente para validar que a migração parece correta. Em alternativa, pode iniciar sposição no portal e verificar as propriedades e recursos para validar que a migração de metadados parece boa.

Se estiver a migrar uma rede virtual, a maioria das configurações de máquinas virtuais não são reiniciadas. Para aplicações nesses VMs, pode validar que a aplicação ainda está em execução.

Pode testar os seus scripts de monitorização e operacionais para ver se os VMs estão a funcionar como esperado e se os seus scripts atualizados funcionam corretamente. Quando os recursos estão no estado pronto, só são suportadas operações OBTER.

Não há uma janela de tempo definida antes da qual você precisa cometer a migração. Pode permanecer neste estado o tempo que quiser. Contudo, o plano de gestão é bloqueado para estes recursos enquanto não abortar ou consolidar.

Se vir erros, pode sempre abortar a migração e regressar ao modelo de implementação clássica. Depois de voltar, o Azure abre as operações de gestão-avião sobre os recursos, para que possa retomar as operações normais nesses VMs no modelo clássico de implementação.

### <a name="abort"></a>Abortar
Este é um passo opcional se quiser reverter as suas alterações para o modelo de implementação clássico e parar a migração. Esta operação elimina os metadados do Gestor de Recursos (criados no passo de preparação) para os seus recursos. 

![Diagrama do passo de aborto](media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Esta operação não pode ser feita depois de teres desencadeado a operação de compromisso.     
>

### <a name="commit"></a>Consolidação
Depois de concluída a validação, pode consolidar a migração. Os recursos já não aparecem no modelo clássico de implementação, e estão disponíveis apenas no modelo de implementação do Gestor de Recursos. Os recursos migrados só podem ser geridos no portal novo.

> [!NOTE]
> Esta é uma operação idempotent. Se falhar, recandidutar a operação. Se continuar a falhar, crie um bilhete de suporte ou crie um fórum no [Microsoft Q&A](/answers/index.html)
>
>

![Diagrama de passo de compromisso](media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Fluxograma de migração

Aqui está um fluxograma que mostra como proceder com a migração:

![Captura de ecrã que mostra os passos da migração](media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Tradução do modelo de implementação clássico para recursos gestores de recursos
Pode encontrar o modelo de implementação clássico e as representações do Gestor de Recursos na tabela seguinte. Atualmente, não são suportadas outras funcionalidades e recursos.

| Representação clássica | Representação do Resource Manager | Notas |
| --- | --- | --- |
| Nome do serviço em nuvem (nome de serviço hospedado) |Nome DNS |Durante a migração, é criado um grupo de recursos novo para cada serviço cloud, com o padrão de nomenclatura `<cloudservicename>-migrated`. Este grupo de recursos contém todos os seus recursos. O nome do serviço cloud converte-se um nome DNS que está associado ao endereço IP público. |
| Máquina virtual |Máquina virtual |As propriedades específicas de cada VM são migradas sem alterações. Certas informações do osProfile, como o nome do computador, não são armazenadas no modelo clássico de implementação, e permanecem vazias após a migração. |
| Recursos de disco ligados à VM |Discos implícitos ligados à VM |Os discos não são modelados como recursos de nível superior no modelo de implementação Resource Manager. São migrados como discos implícitos na VM. Atualmente, apenas os discos que estão ligados a uma VM são suportados. Os VMs do Gestor de Recursos podem agora utilizar contas de armazenamento no modelo clássico de implementação, o que permite que os discos sejam facilmente migrados sem quaisquer atualizações. |
| Extensões de VM |Extensões de VM |Todas as extensões de recursos, exceto as extensões XML, são migradas do modelo de implementação clássica. |
| Certificados de máquinas virtuais |Certificados no Azure Key Vault |Se um serviço de nuvem contiver certificados de serviço, a migração cria um novo cofre chave Azure por serviço na nuvem, e move os certificados para o cofre chave. As VMs são atualizadas, de modo a fazerem referência aos certificados no cofre de chaves. <br><br> Não apague o cofre da chave. Isto pode fazer com que o VM entre num estado falhado. |
| Configuração WinRM |Configuração WinRM em osProfile |A configuração Gestão Remota do Windows é movida sem quaisquer alterações como parte da migração. |
| Propriedade Availability-set |Recurso Availability-set | A especificação definida pela disponibilidade é uma propriedade no VM no modelo de implementação clássico. Como parte da migração, os conjuntos de disponibilidade convertem-se num recurso de nível superior. As configurações seguintes não são suportadas: múltiplos conjuntos de disponibilidade por serviço cloud, um ou mais conjuntos de disponibilidade juntamente com VMs que não estão em nenhum conjunto de disponibilidade num serviço cloud. |
| Configuração de rede numa VM |Interface de rede primária |A configuração de rede numa VM é representada como o recurso de interface de rede primária após a migração. Nas VMs que não estão numa rede virtual, o endereço IP é alterado durante a migração. |
| Várias interfaces de rede numa VM |Interfaces de rede |Se um VM tiver múltiplas interfaces de rede associadas a ele, cada interface de rede torna-se um recurso de alto nível como parte da migração, juntamente com todas as propriedades. |
| Conjunto de pontos finais com balanceamento de carga |Balanceador de carga |No modelo de implementação clássica, a plataforma atribuía um balanceador de carga implícito a cada serviço cloud. Durante a migração, é criado um recurso de balanceador de carga novo e o conjunto de pontos finais com balanceamento de carga converte-se em regras de balanceador de carga. |
| Regras NAT de entrada |Regras NAT de entrada |Os pontos finais de entrada definidos na VM são convertidos em regras de tradução de endereços de rede de entrada no balanceador de carga durante a migração. |
| Endereço VIP |Endereço IP público com o nome DNS |O endereço IP virtual torna-se um endereço IP público e está associado ao equilibrador de carga. Os IPs virtuais só podem ser migrados se lhes tiverem sido atribuídos pontos finais de entrada. |
| Rede virtual |Rede virtual |A rede virtual é migrada, com todas as propriedades, para o modelo de implementação Resource Manager. É criado um grupo de recursos novo com o nome `-migrated`. |
| IPs Reservados |Endereço IP público com o método de alocação estática |Os IPs Reservados associados ao balanceador de carga são migrados, juntamente com a migração do serviço cloud ou da máquina virtual. Os IPs reservados não associados podem ser migrados utilizando [o Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip).  |
| Endereço IP público por VM |Endereço IP público com o método de alocação dinâmica |O endereço IP público associado à VM é convertido num recurso de endereço IP público, com o método de alocação definido como estático. |
| NSGs |NSGs |Os grupos de segurança de rede associados a uma sub-rede são clonados como parte da migração para o modelo de implementação Resource Manager. O NSG no modelo de implementação clássica não é removido durante a migração. No entanto, as operações do plano de gestão para o NSG são bloqueadas enquanto a migração está em curso. Os NSGs não associados podem ser migrados usando [o Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup).|
| Servidores DNS |Servidores DNS |Os servidores DNS associados a uma rede virtual ou à VM são migrados como parte da migração de recursos correspondente, juntamente com todas as propriedades. |
| UDRs |UDRs |As rotas definidas pelo utilizador associadas a uma sub-rede são clonados como parte da migração para o modelo de implementação Resource Manager. O URD no modelo de implementação clássica não é removido durante a migração. As operações do plano de gestão para o UDR são bloqueadas enquanto a migração está em curso. Os UDRs não associados podem ser migrados utilizando [o Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable). |
| Propriedade de reencaminhamento de IPs na configuração de rede de uma VM |Propriedade de reencaminhamento de IPs na NIC |A propriedade de reencaminhamento de IPs numa VM é convertida numa propriedade na interface de rede durante a migração. |
| Balanceador de carga com vários IPs |Balanceador de carga com vários recursos de IP público |Cada IP público associado ao equilibrador de carga é convertido num recurso IP público e associado ao equilibrador de carga após a migração. |
| Nomes DNS internos na VM |Nomes DNS internos na NIC |Durante a migração, os sufixos de DNS internos das VMs são migrados para uma propriedade só de leitura com o nome “InternalDomainNameSuffix” na NIC. O sufixo permanece inalterado após a migração, e a resolução de VM deve continuar a funcionar como anteriormente. |
| Gateway de rede virtual |Gateway de rede virtual |As propriedades do gateway de rede virtual são migradas inalteradas. O VIP associado ao gateway também não muda. |
| Site de rede local |Gateway de rede local |As propriedades do site da rede local são migradas inalteradas para um novo recurso chamado gateway de rede local. Isto representa prefixos de endereço no local e o IP de gateway remoto. |
| Referências de ligações |Ligação |As referências de conectividade entre o gateway e o site de rede local na configuração da rede são representadas por um novo recurso chamado Conexão. Todas as propriedades de referência de conectividade em ficheiros de configuração de rede são copiadas inalteradamente para o recurso Connection. A conectividade entre redes virtuais no modelo clássico de implementação é conseguida através da criação de dois túneis IPsec para sites de rede locais que representam as redes virtuais. Isto é transformado para o tipo de ligação virtual rede-virtual no modelo Gestor de Recursos, sem necessitar de portais de rede locais. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Alterações à automatização e às ferramentas após a migração
Como parte da migração dos seus recursos do modelo de implementação clássico para o modelo de implementação do Gestor de Recursos, tem de atualizar a automatização ou ferramenta existente para garantir que continua a funcionar após a migração.


## <a name="next-steps"></a>Próximos passos

* [Visão geral da migração suportada pela plataforma de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Use o PowerShell para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Utilize o CLI para migrar os recursos iaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [VPN Gateway clássico para migração de Gestor de Recursos](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [Migrar circuitos ExpressRoute e redes virtuais associadas do clássico ao modelo de implementação do Gestor de Recursos](../expressroute/expressroute-migration-classic-resource-manager.md)
* [Ferramentas comunitárias para ajudar na migração de recursos iaas do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)
* [Reveja as perguntas mais frequentes sobre a migração dos recursos da IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md)
