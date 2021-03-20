---
title: Soluções Oráculos em máquinas virtuais Azure | Microsoft Docs
description: Saiba mais sobre configurações suportadas e limitações de imagens de máquinas virtuais Oracle no Microsoft Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.openlocfilehash: 2f34e0bb3c4abcf4efba807f95decd798bbc1f86
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669073"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Imagens Oracle VM e sua implementação no Microsoft Azure

Este artigo abrange informações sobre soluções Oracle baseadas em imagens de máquinas virtuais publicadas pela Oracle no Mercado Azure. Se estiver interessado em soluções de aplicações cross-cloud com a Oracle Cloud Infrastructure, consulte [soluções de aplicações oracle que integram o Microsoft Azure e a Oracle Cloud Infrastructure.](oracle-oci-overview.md)

Para obter uma lista de imagens atualmente disponíveis, execute o seguinte comando:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

A partir de junho de 2020 estão disponíveis as seguintes imagens:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

Estas imagens são consideradas "Traga a sua própria licença" e, como tal, só será cobrado pelos custos de computação, armazenamento e networking incorridos pela execução de um VM.  Presume-se que está devidamente licenciado para usar o software Oracle e que tem um acordo de suporte atual em vigor com a Oracle. A Oracle garantiu a mobilidade de licenças de instalações para Azure. Consulte a nota publicada [da Oracle e Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) para obter mais informações sobre a mobilidade das licenças.

Os indivíduos também podem optar por basear as suas soluções numa imagem personalizada que criam de raiz em Azure ou carregar uma imagem personalizada a partir do ambiente de instalações.

## <a name="oracle-database-vm-images"></a>Imagens VM da base de dados do Oráculo

A Oracle suporta a execução da Oracle Database 12.1 e as edições Standard e Enterprise mais altas em Azure em imagens de máquinas virtuais baseadas no Oracle Linux.  Para obter o melhor desempenho para a produção de cargas de trabalho da Oracle Database on Azure, certifique-se de que dimensiona corretamente a imagem VM e utiliza discos geridos Premium SSD ou Ultra SSD. Para obter instruções sobre como obter rapidamente uma Base de Dados Oráculo em funcionamento em Azure utilizando a imagem VM publicada pela Oracle, [experimente a passagem rápida da Oracle Database Quickstart](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opções de configuração de disco anexadas

Os discos anexos dependem do serviço de armazenamento Azure Blob. Cada disco padrão é capaz de um máximo teórico de aproximadamente 500 operações de entrada/saída por segundo (IOPS). A nossa oferta de disco premium é preferida para cargas de dados de alto desempenho e pode atingir até 5000 IOps por disco. Pode utilizar um único disco se satisfaça as suas necessidades de desempenho. No entanto, pode melhorar o desempenho eficaz do IOPS se utilizar vários discos anexos, espalhar dados de base de dados através deles e, em seguida, utilizar a Oracle Automatic Storage Management (ASM). Consulte a [visão geral do Armazenamento Automático da Oracle](https://www.oracle.com/technetwork/database/index-100339.html) para obter mais informações específicas da Oracle ASM. Para um exemplo de como instalar e configurar o Oracle ASM num Linux Azure VM, consulte o tutorial [de Gestão automatizada de Armazenamento Automatizado da Oracle.](configure-oracle-asm.md)

### <a name="shared-storage-configuration-options"></a>Opções de configuração de armazenamento compartilhado

O Azure NetApp Files foi projetado para satisfazer os requisitos fundamentais de executar cargas de trabalho de alto desempenho, como bases de dados na nuvem, e fornece;

- Azure nativo compartilhado serviço de armazenamento NFS para executar cargas de trabalho Oracle quer através de VM nativo cliente NFS, ou Oracle dNFS
- Níveis de desempenho escaláveis que refletem a gama real de exigências do IOPS
- Baixa latência
- Alta disponibilidade, alta durabilidade e gestão em escala, tipicamente exigida por cargas de trabalho de empresas críticas da missão (como SAP e Oracle)
- Backup e recuperação rápidos e eficientes, para alcançar os RTO e RPO SLA mais agressivos

Estas capacidades são possíveis porque o Azure NetApp Files é baseado no NetApp® ONTAP® sistemas de flash totalmente ligados dentro do ambiente do Centro de Dados Azure – como um serviço Azure Native. O resultado é uma tecnologia ideal de armazenamento de bases de dados que pode ser a provisionada e consumida tal como outras opções de armazenamento Azure. Consulte [a documentação do Azure NetApp Files](../../../azure-netapp-files/index.yml) para obter mais informações sobre como implementar e aceder aos volumes NFS dos Ficheiros NFS do Azure NetApp. Consulte [o Oracle no Guia de Boas Práticas de Implementação do Azure Utilizando ficheiros Azure NetApp](https://www.netapp.com/us/media/tr-4780.pdf) para obter recomendações de boas práticas para operar uma base de dados oracle em ficheiros Azure NetApp.

## <a name="licensing-oracle-database--software-on-azure"></a>Licenciamento Oracle Database & software em Azure

O Microsoft Azure é um ambiente em nuvem autorizado para executar a Oracle Database. A tabela Oracle Core Fator não é aplicável ao licenciar bases de dados oracle na nuvem. Em vez disso, ao utilizar VMs com tecnologia Hyper-Threading ativada para bases de dados da Enterprise Edition, conte dois vCPUs como equivalente a uma licença de processador Oracle se estiver ativado a hiper-leitura (conforme indicado no documento de política). Os detalhes da apólice podem ser [consultados aqui.](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)
As bases de dados do Oráculo geralmente requerem memória mais alta e IO. Por esta razão, [recomenda-se vMs otimizados de memória](../../sizes-memory.md) para estas cargas de trabalho. Para otimizar ainda mais as suas cargas de trabalho, os [vCPUs do Núcleo Constrangido](../../constrained-vcpu.md) são recomendados para as cargas de trabalho da Oracle Database que requerem alta memória, armazenamento e largura de banda de I/O, mas não uma contagem elevada de núcleo.

Ao migrar o software e cargas de trabalho da Oracle de instalações para o Microsoft Azure, a Oracle fornece mobilidade de licença, conforme indicado no [Oráculo em Azure FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)

## <a name="high-availability-and-disaster-recovery-considerations"></a>Elevada disponibilidade e considerações de recuperação de desastres

Ao utilizar bases de dados oracle em Azure, você é responsável pela implementação de uma solução de alta disponibilidade e recuperação de desastres para evitar qualquer tempo de inatividade.

A alta disponibilidade e recuperação de desastres para a Oracle Database Enterprise Edition (sem depender do Oracle RAC) pode ser alcançada no Azure usando [Data Guard, Ative Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html), ou Oracle [GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), com duas bases de dados em duas máquinas virtuais separadas. Ambas as máquinas virtuais devem estar na mesma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que podem aceder umas às outras durante o endereço IP persistente privado.  Além disso, recomendamos colocar as máquinas virtuais no mesmo conjunto de disponibilidade para permitir que a Azure as coloque em domínios de avaria separados e domínios de atualização. Se quiser ter geo-redundância, crie as duas bases de dados para replicar entre duas regiões diferentes e ligue as duas instâncias a uma Gateway VPN.

O tutorial [Implement Oracle Data Guard on Azure](configure-oracle-dataguard.md) acompanha-o através do procedimento básico de configuração em Azure.  

Com a Oracle Data Guard, a alta disponibilidade pode ser alcançada com uma base de dados primária numa máquina virtual, uma base de dados secundária (standby) noutra máquina virtual, e uma replicação unidirecional entre elas. O resultado é ler o acesso à cópia da base de dados. Com o Oracle GoldenGate, pode configurar a replicação bidis entre as duas bases de dados. Para aprender a configurar uma solução de alta disponibilidade para as suas bases de dados utilizando estas ferramentas, consulte a [ative data guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) e a documentação [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) no site da Oracle. Se precisar de ler-escrever acesso à cópia da base de dados, pode utilizar a [Oracle Ative Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

O tutorial [Implement Oracle GoldenGate on Azure](configure-oracle-golden-gate.md) acompanha-o através do procedimento básico de configuração em Azure.

Além de ter uma solução HA e DR arquivada em Azure, deverá ter uma estratégia de backup no local para restaurar a sua base de dados. O tutorial [backup e recuperar uma Base de Dados Oráculos](./oracle-overview.md) acompanha-o através do procedimento básico para estabelecer uma cópia de segurança consistente.

## <a name="support-for-jd-edwards"></a>Apoio a JD Edwards

De acordo com a nota de suporte da Oracle [Doc ID 2178595.1,](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)as versões JD Edwards EnterpriseOne 9.2 e acima são suportadas em **qualquer oferta de nuvem pública** que cumpra a sua especificidade `Minimum Technical Requirements` (MTR).  É necessário criar imagens personalizadas que cumpram as suas especificações DEMR para compatibilidade de aplicações de SISTEMA e software.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Oracle WebLogic Server oferece máquina virtual

A Oracle e a Microsoft estão a colaborar para levar o WebLogic Server ao Mercado Azure sob a forma de uma coleção de ofertas de aplicações Azure.  Estas ofertas são descritas no artigo Aplicações Oracle [WebLogic Server Azure](oracle-weblogic.md).

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Imagens de máquinas virtuais do Oracle WebLogic Server

- **O clustering é suportado apenas na Enterprise Edition.** Está licenciado para utilizar o clustering WebLogic apenas quando utilizar a Enterprise Edition do Oracle WebLogic Server. Não utilize o agrupamento com a Oracle WebLogic Server Standard Edition.
- **A multicast UDP não é suportada.** O Azure suporta o unicasting da UDP, mas não o multicasting ou a radiodifusão. O Oracle WebLogic Server é capaz de confiar nas capacidades unicast do Azure UDP. Para obter os melhores resultados baseados no unicast UDP, recomendamos que o tamanho do cluster WebLogic seja mantido estático, ou mantido com não mais de 10 servidores geridos.
- **O Oracle WebLogic Server espera que as portas públicas e privadas sejam as mesmas para o acesso T3 (por exemplo, quando se utiliza a Enterprise JavaBeans).** Considere um cenário de vários níveis onde uma aplicação de camada de serviço (EJB) está em execução num cluster Oracle WebLogic Server composto por dois ou mais VMs, numa rede virtual chamada *SLWLS*. O nível de cliente está numa sub-rede diferente na mesma rede virtual, executando um simples programa Java tentando ligar para o EJB na camada de serviço. Como é necessário carregar o equilíbrio da camada de serviço, é necessário criar um ponto final equilibrado em carga pública para as máquinas virtuais do cluster Oracle WebLogic Server. Se a porta privada que especifica for diferente da porta pública (por exemplo, 7006:7008), ocorre um erro como o seguinte:

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   Isto porque, para qualquer acesso remoto t3, o Oracle WebLogic Server espera que a porta do balanceador de carga e a porta do servidor gerido pela WebLogic sejam as mesmas. No caso anterior, o cliente está a aceder à porta 7006 (a porta do balançador de carga) e o servidor gerido está a ouvir no 7008 (a porta privada). Esta restrição aplica-se apenas ao acesso T3, não http.

   Para evitar este problema, utilize uma das seguintes soluções alternativas:

- Utilize os mesmos números portuários privados e públicos para pontos finais equilibrados de carga dedicados ao acesso T3.
- Inclua o seguinte parâmetro JVM ao iniciar o Oracle WebLogic Server:

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

Para obter informações relacionadas, consulte o artigo **KB 860340.1** em <https://support.oracle.com> .

- **Limitações dinâmicas de agrupamento e de equilíbrio de carga.** Suponha que pretende usar um cluster dinâmico no Oracle WebLogic Server e expô-lo através de um único ponto final de carga pública equilibrado em Azure. Isto pode ser feito desde que utilize um número de porta fixo para cada um dos servidores geridos (não atribuídos dinamicamente a partir de um intervalo) e não inicie mais servidores geridos do que as máquinas que o administrador está a seguir. Ou seja, não há mais do que um servidor gerido por máquina virtual). Se a sua configuração resultar no início de mais Servidores WebLogic oracle do que em máquinas virtuais (isto é, onde várias instâncias do Oracle WebLogic Server partilham a mesma máquina virtual), então não é possível que mais de uma dessas instâncias de Oracle WebLogic Servers se ligue a um determinado número de porta. Os outros na máquina virtual falham.

   Se configurar o servidor de administração para atribuir automaticamente números de porta únicos aos seus servidores geridos, então o equilíbrio de carga não é possível porque o Azure não suporta mapeamento de uma única porta pública para várias portas privadas, como seria necessário para esta configuração.
- **Múltiplas instâncias do Oracle WebLogic Server numa máquina virtual.** Dependendo dos requisitos da sua implementação, poderá considerar executar várias instâncias do Oracle WebLogic Server na mesma máquina virtual, se a máquina virtual for suficientemente grande. Por exemplo, numa máquina virtual de tamanho médio, que contém dois núcleos, pode optar por executar duas instâncias do Oracle WebLogic Server. No entanto, recomendamos que evite introduzir pontos únicos de falha na sua arquitetura, o que seria o caso se usasse apenas uma máquina virtual que está a executar várias instâncias do Oracle WebLogic Server. A utilização de pelo menos duas máquinas virtuais poderia ser uma melhor abordagem, e cada máquina virtual poderia então executar várias instâncias do Oracle WebLogic Server. Cada instância do Oracle WebLogic Server ainda pode fazer parte do mesmo cluster. No entanto, atualmente não é possível utilizar o Azure para equilibrar pontos finais que são expostos por tais implementações do Oracle WebLogic Server dentro da mesma máquina virtual, porque o equilibrador de carga Azure requer que os servidores equilibrados de carga sejam distribuídos por máquinas virtuais únicas.

## <a name="oracle-jdk-virtual-machine-images"></a>Imagens de máquina virtual Oracle JDK

- **JDK 6 e 7 últimas atualizações.** Embora recomendemos a utilização da versão mais recente e suportada de Java (atualmente Java 8), o Azure também disponibiliza imagens JDK 6 e 7. Isto destina-se a aplicações antigas que ainda não estão prontas para serem atualizadas para JDK 8. Embora as atualizações para imagens anteriores do JDK possam já não estar disponíveis para o público em geral, dada a parceria da Microsoft com a Oracle, as imagens JDK 6 e 7 fornecidas pela Azure destinam-se a conter uma atualização não pública mais recente que é normalmente oferecida pela Oracle a apenas um grupo selecionado de clientes apoiados pela Oracle. Novas versões das imagens JDK serão disponibilizadas ao longo do tempo com lançamentos atualizados de JDK 6 e 7.

   O JDK disponível nas imagens JDK 6 e 7, e as máquinas virtuais e imagens derivadas delas, só podem ser usadas dentro do Azure.
- **JDK de 64 bits.** As imagens de máquinas virtuais do Oracle WebLogic Server e as imagens de máquina virtual Oracle JDK fornecidas pelo Azure contêm as versões de 64 bits tanto do Windows Server como do JDK.

## <a name="next-steps"></a>Passos seguintes

Tem agora uma visão geral das atuais soluções Oracle baseadas em imagens de máquinas virtuais no Microsoft Azure. O seu próximo passo é implantar a sua primeira base de dados oracle no Azure.

> [!div class="nextstepaction"]
> [Criar uma base de dados oracle em Azure](oracle-database-quick-create.md)
