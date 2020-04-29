---
title: Soluções Oracle em máquinas virtuais Azure [ Microsoft Docs
description: Conheça as configurações suportadas e limitações das imagens de máquinas virtuais Oracle no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: borisb
ms.openlocfilehash: 0cee7c25960d567c75a14d8ad9ef95b3e7221862
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683422"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Imagens VM da Oracle e sua implementação no Microsoft Azure

Este artigo cobre informações sobre soluções Oracle baseadas em imagens de máquinas virtuais publicadas pela Oracle no Azure Marketplace. Se estiver interessado em soluções de aplicação cross-cloud com a Oracle Cloud Infrastructure, consulte soluções de [aplicação Oracle que integram a Microsoft Azure e a Oracle Cloud Infrastructure.](oracle-oci-overview.md)

Para obter uma lista das imagens atualmente disponíveis, executar o seguinte comando:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

A partir de maio de 2019 estão disponíveis as seguintes imagens:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Estas imagens são consideradas "Bring Your Own License" e, como tal, só serão cobradas pelos custos de computação, armazenamento e networking incorridos com a execução de um VM.  Presume-se que está devidamente licenciado para usar o software Oracle e que tem um acordo de suporte atual com a Oracle. A Oracle garantiu a mobilidade de licença saindo do local para O Azure. Consulte a nota publicada da [Oracle e microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) para obter detalhes sobre a mobilidade da licença. 

Os indivíduos também podem optar por basear as suas soluções numa imagem personalizada que criam de raiz no Azure ou carregar uma imagem personalizada a partir do seu ambiente no local.

## <a name="oracle-database-vm-images"></a>Imagens VM da base de dados Oracle
A Oracle suporta a execução da Oracle Database 12.1 e edições mais altas da Standard e Enterprise em Azure em imagens de máquinas virtuais baseadas no Oracle Linux.  Para obter o melhor desempenho para cargas de trabalho de produção da Oracle Database on Azure, certifique-se de dimensionar adequadamente a imagem VM e utilizar Discos Geridos Premium SSD ou Ultra SSD. Para obter instruções sobre como obter rapidamente uma Base de Dados Oracle em Funcionamento em Azure utilizando a imagem VM publicada pela Oracle, experimente a passagem rápida [da Oracle Database](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opções de configuração de disco anexadas

Os discos anexados dependem do serviço de armazenamento Azure Blob. Cada disco padrão é capaz de um máximo teórico de aproximadamente 500 operações de entrada/saída por segundo (IOPS). A nossa oferta de disco premium é preferida para cargas de trabalho de base de dados de alto desempenho e pode atingir até 5000 IOps por disco. Pode utilizar um único disco se isso satisfizer as suas necessidades de desempenho. No entanto, pode melhorar o desempenho eficaz do IOPS se utilizar vários discos anexados, espalhar dados da base de dados através deles e, em seguida, utilizar a Oracle Automatic Storage Management (ASM). Consulte a visão geral do [Armazenamento Automático oracle](https://www.oracle.com/technetwork/database/index-100339.html) para obter mais informações específicas da Oracle ASM. Para um exemplo de como instalar e configurar o Oracle ASM num VM Linux Azure, consulte o tutorial de [Gestão automática](configure-oracle-asm.md) de Armazenamento do Oracle.

### <a name="shared-storage-configuration-options"></a>Opções de configuração de armazenamento partilhada

O Azure NetApp Files foi concebido para satisfazer os requisitos fundamentais de executar cargas de trabalho de alto desempenho, como bases de dados na nuvem, e fornece;
- Serviço de armazenamento NFS partilhado nativo azure para executar cargas de trabalho Oracle, seja através do cliente nativo DaFS vM, ou do Oracle dNFS
- Níveis de desempenho escaláveis que refletem a gama real de exigências do IOPS
- Baixa latência
- Elevada disponibilidade, elevada durabilidade e gestão em escala, tipicamente exigida seletivamente por cargas de trabalho críticas da missão (como SAP e Oracle)
- Backup e recuperação rápidos e eficientes, para alcançar o RTO mais agressivo e RPO SLA's

Estas capacidades são possíveis porque o Azure NetApp Files é baseado no NetApp® ONTAP® sistemas de flash que funciona dentro do ambiente do centro de dados Azure – como um serviço Azure Native. O resultado é uma tecnologia de armazenamento de base de dados ideal que pode ser aprovisionada e consumida como outras opções de armazenamento Azure. Consulte a documentação do [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) para obter mais informações sobre como implementar e aceder aos volumes NFS do Azure NetApp Files. Consulte o Oracle no Guia de Boas Práticas de [Implementação do Azure utilizando ficheiros Azure NetApp](https://www.netapp.com/us/media/tr-4780.pdf) para recomendações de boas práticas para operar uma base de dados da Oracle em Ficheiros Azure NetApp.


## <a name="licensing-oracle-database--software-on-azure"></a>Licenciamento Oracle Base de Dados & software em Azure
O Microsoft Azure é um ambiente de nuvem autorizado para executar a Oracle Database. A tabela Oracle Core Fator não é aplicável ao licenciar bases de dados oracle na nuvem. Em vez disso, ao utilizar VMs com tecnologia hiper-threading ativada para bases de dados da Enterprise Edition, conte dois vCPUs como equivalentes a uma licença do Processador Oracle se o hiperthreading estiver ativado (conforme indicado no documento de política). Os detalhes da política podem ser encontrados [aqui.](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)
As bases de dados da Oracle geralmente requerem memória mais alta e IO. Por esta razão, são [recomendados VMs otimizados](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) de memória para estas cargas de trabalho. Para otimizar ainda mais as suas cargas de trabalho, os [vCPUs do Núcleo Constrangido](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu) são recomendados para cargas de trabalho da Oracle Database que requerem alta memória, armazenamento e largura de banda em I/S, mas não uma contagem de núcleo elevada.

Ao migrar o software da Oracle e as cargas de trabalho das instalações para o Microsoft Azure, a Oracle proporciona mobilidade de licença, conforme indicado no [Oracle em Azure FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)


## <a name="oracle-real-application-cluster-oracle-rac"></a>Cluster de aplicações real oracle (Oracle RAC)
O Oracle Real Application Cluster (Oracle RAC) foi concebido para atenuar a falha de um único nó numa configuração de cluster multi-nó no local. Baseia-se em duas tecnologias no local que não são nativas de ambientes de nuvem pública hiper-escala: rede multi-elenco e disco partilhado. Se a sua solução de base de dados necessitar de Oracle RAC em Azure, precisa de software de terceiros=partes para ativar estas tecnologias. Para obter mais informações sobre o Oracle RAC, consulte a [página FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações de elevada disponibilidade e recuperação de desastres
Ao utilizar bases de dados Oracle em Azure, é responsável por implementar uma solução de alta disponibilidade e recuperação de desastres para evitar qualquer tempo de inatividade. 

A elevada disponibilidade e recuperação de desastres para a Oracle Database Enterprise Edition (sem depender da Oracle RAC) pode ser alcançada no Azure usando [a Data Guard, Ative Data Guard,](https://www.oracle.com/database/technologies/high-availability/dataguard.html)ou [Oracle GoldenGate,](https://www.oracle.com/technetwork/middleware/goldengate)com duas bases de dados em duas máquinas virtuais separadas. Ambas as máquinas virtuais devem estar na mesma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que podem aceder entre si ao longo do endereço IP persistente privado.  Além disso, recomendamos a colocação das máquinas virtuais no mesmo conjunto de disponibilidade para permitir que o Azure as coloque em domínios de avaria separados e atualize domínios. Se quiser ter geo-redundância, instale as duas bases de dados para replicar entre duas regiões diferentes e ligue as duas instâncias com um Gateway VPN.

O tutorial [Implement O Oráculo Data Guard em Azure](configure-oracle-dataguard.md) acompanha-o através do procedimento básico de configuração em Azure.  

Com a Oracle Data Guard, a alta disponibilidade pode ser alcançada com uma base de dados primária numa máquina virtual, uma base de dados secundária (standby) noutra máquina virtual e replicação de sentido único entre eles. O resultado é ler o acesso à cópia da base de dados. Com o Oracle GoldenGate, pode configurar a replicação bidirecional entre as duas bases de dados. Para saber como configurar uma solução de alta disponibilidade para as suas bases de dados utilizando estas ferramentas, consulte a documentação [Ative Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) e [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) no site da Oracle. Se necessitar de acesso à cópia da base de dados, pode utilizar o [Oracle Ative Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

O tutorial [Implement Oracle GoldenGate em Azure](configure-oracle-golden-gate.md) acompanha-o através do procedimento básico de configuração em Azure.

Além de ter uma solução HA e DR arquitetada em Azure, deverá ter uma estratégia de backup em vigor para restaurar a sua base de dados. O tutorial [Backup e recuperar uma Base](oracle-backup-recovery.md) de Dados Oracle acompanha-o através do procedimento básico para estabelecer um backup consistente.


## <a name="support-for-jd-edwards"></a>Apoio a JD Edwards
De acordo com a nota de suporte da Oracle [Doc ID 2178595.1,](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)as versões JD `Minimum Technical Requirements` Edwards EnterpriseOne 9.2 ou superior são suportadas em qualquer oferta de nuvem **pública** que responda ao seu específico (MTR).  Você precisa criar imagens personalizadas que vão ao encontro das suas especificações MTR para a compatibilidade de aplicações de SISTEMA e software. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Imagens de máquinavirtual do Servidor WebLogic oracle

* **O clustering é suportado apenas na Enterprise Edition.** Você está licenciado para usar o clusterWebLogic apenas quando usar a Edição Enterprise do Servidor WebLogic Oracle. Não utilize o clustering com a Oracle WebLogic Server Standard Edition.
* **O multicast UDP não é suportado.** O Azure suporta o unicasting uDP, mas não multicasting ou radiodifusão. O Oracle WebLogic Server é capaz de confiar nas capacidades unicast azure UDP. Para obter os melhores resultados baseados no unicast UDP, recomendamos que o tamanho do cluster WebLogic seja mantido estático, ou mantido com não mais de 10 servidores geridos.
* **O Oracle WebLogic Server espera que as portas públicas e privadas sejam as mesmas para o acesso T3 (por exemplo, ao utilizar a Enterprise JavaBeans).** Considere um cenário de vários níveis em que uma aplicação de camada de serviço (EJB) está em execução num cluster oracle WebLogic Server composto por dois ou mais VMs, numa rede virtual chamada *SLWLS*. O nível de cliente está numa subrede diferente na mesma rede virtual, executando um simples programa Java tentando chamar EJB na camada de serviço. Como é necessário carregar o equilíbrio da camada de serviço, é necessário criar um ponto final equilibrado em carga pública para as máquinas virtuais do cluster Oracle WebLogic Server. Se o porto privado que especifica for diferente do porto público (por exemplo, 7006:7008), ocorre um erro como o seguinte:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Isto porque, para qualquer acesso remoto ao T3, o Oracle WebLogic Server espera que a porta de equilíbrio de carga e a porta de servidor gerida pela WebLogic sejam as mesmas. No caso anterior, o cliente está a aceder à porta 7006 (a porta de equilíbrio de carga) e o servidor gerido está a ouvir no 7008 (a porta privada). Esta restrição é aplicável apenas para acesso T3, e não HTTP.

   Para evitar este problema, utilize uma das seguintes suposições:

  * Utilize os mesmos números de portas privadas e públicas para carregar pontos finais equilibrados dedicados ao acesso T3.
  * Inclua o seguinte parâmetro JVM ao iniciar o Oracle WebLogic Server:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Para obter informações relacionadas, consulte o artigo <https://support.oracle.com> **KB 860340.1** em .

* **Limitações dinâmicas de clustering e equilíbrio de carga.** Suponha que queira usar um cluster dinâmico no Oracle WebLogic Server e expô-lo através de um único ponto final equilibrado em carga pública em Azure. Isto pode ser feito desde que utilize um número de porta fixo para cada um dos servidores geridos (não atribuído dinamicamente a partir de uma gama) e não inicie servidores mais geridos do que as máquinas que o administrador está a seguir. Ou seja, não há mais do que um servidor gerido por máquina virtual). Se a sua configuração resultar em mais servidores Oracle WebLogic sendo iniciados do que há máquinas virtuais (isto é, onde várias instâncias do Oracle WebLogic Server partilham a mesma máquina virtual), então não é possível que mais do que uma dessas instâncias de Servidores WebLogic oracle se ligue a um determinado número de porta. Os outros naquela máquina virtual falham.

   Se configurar o servidor de administração para atribuir automaticamente números de porta únicos aos seus servidores geridos, então o equilíbrio de carga não é possível porque o Azure não suporta o mapeamento de uma única porta pública para várias portas privadas, como seria necessário para esta configuração.
* **Múltiplas instâncias do Oracle WebLogic Server numa máquina virtual.** Dependendo dos requisitos da sua implementação, pode considerar executar várias instâncias do Oracle WebLogic Server na mesma máquina virtual, se a máquina virtual for suficientemente grande. Por exemplo, numa máquina virtual de tamanho médio, que contém dois núcleos, pode optar por executar duas instâncias do Oracle WebLogic Server. No entanto, recomendamos que evite introduzir pontos únicos de falha na sua arquitetura, o que seria o caso se usasse apenas uma máquina virtual que está a executar várias instâncias do Oracle WebLogic Server. A utilização de pelo menos duas máquinas virtuais poderia ser uma melhor abordagem, e cada máquina virtual poderia então executar várias instâncias do Oracle WebLogic Server. Cada instância do Oracle WebLogic Server ainda pode fazer parte do mesmo cluster. No entanto, atualmente não é possível utilizar o Azure para equilibrar pontos finais de equilíbrio de carga que são expostos por tais implementações do Oracle WebLogic Server dentro da mesma máquina virtual, porque o equilíbrio de carga Azure exige que os servidores equilibrados em carga sejam distribuídos por máquinas virtuais únicas.

## <a name="oracle-jdk-virtual-machine-images"></a>Imagens de máquina virtual Oracle JDK
* **JDK 6 e 7 últimas atualizações.** Embora recomendamos a utilização da mais recente versão pública e suportada de Java (atualmente Java 8), o Azure também disponibiliza imagens JDK 6 e 7. Isto destina-se a aplicações antigas que ainda não estão prontas para serem atualizadas para JDK 8. Embora as atualizações para imagens anteriores do JDK possam deixar de estar disponíveis para o público em geral, dada a parceria da Microsoft com a Oracle, as imagens JDK 6 e 7 fornecidas pelo Azure destinam-se a conter uma atualização não pública mais recente que normalmente é oferecida pela Oracle a penas que um grupo selecionado de clientes apoiados pela Oracle. Novas versões das imagens JDK serão disponibilizadas ao longo do tempo com lançamentos atualizados de JDK 6 e 7.

   O JDK disponível nas imagens JDK 6 e 7, e as máquinas virtuais e imagens derivadas delas, só podem ser utilizados dentro do Azure.
* **64 bits JDK.** As imagens de máquinavirtual do Oracle WebLogic Server e as imagens de máquinavirtual Oracle JDK fornecidas pelo Azure contêm as versões de 64 bits tanto do Windows Server como do JDK.

## <a name="next-steps"></a>Passos seguintes
Tem agora uma visão geral das soluções atuais da Oracle baseadas em imagens de máquinas virtuais no Microsoft Azure. O seu próximo passo é implementar a sua primeira base de dados Oracle no Azure.

> [!div class="nextstepaction"]
> [Crie uma base de dados da Oracle em Azure](oracle-database-quick-create.md)
