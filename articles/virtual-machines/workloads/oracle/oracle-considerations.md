---
title: Soluções da Oracle no Microsoft Azure | Documentos da Microsoft
description: Saiba mais sobre as configurações suportadas e limitações de soluções da Oracle no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/02/2018
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 945ba9b2ba4dbc22941ca6b105417f591f2dd837
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012758"
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Soluções da Oracle e a respetiva implementação no Microsoft Azure
Este artigo aborda as informações necessárias para implementar várias soluções de Oracle no Microsoft Azure com êxito. Estas soluções são baseadas em imagens de Máquina Virtual publicadas da Oracle no Azure Marketplace. Para obter uma lista de imagens atualmente disponíveis, execute o seguinte comando:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
A partir de 6-16-2017 a lista de imagens são os seguintes:
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Estas imagens são consideradas "Traga a sua própria licença" e assim apenas serão cobrados para computação, armazenamento e os custos de funcionamento em rede incorridos pela execução de uma VM.  Pressupõe-se que está corretamente licenciado para utilizar Oracle software e de que tem um contrato de suporte atual no local com a Oracle. Oracle tem garantia de mobilidade de licenças no local para o Azure. Consulte o publicados [Oracle e Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) nota para obter detalhes sobre a mobilidade de licenças. 

Indivíduos também podem optar por basear suas soluções numa imagem personalizada que criar do zero no Azure ou carregar uma imagem personalizada do ambiente no local.

## <a name="support-for-jd-edwards"></a>Suporte para JD Edwards
De acordo com a observação de suporte da Oracle [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) , JD Edwards EnterpriseOne versões 9.2 e superior, são suportadas no **oferta de cloud de qualquer pública** que atenda às suas específicas `Minimum Technical Requirements` (MTR).  Precisa criar imagens personalizadas que atendam a suas especificações de MTR para compatibilidade de aplicativos do sistema operacional e software. 

## <a name="oracle-database-virtual-machine-images"></a>Imagens de máquina virtual do Oracle da base de dados
Oracle suporta edições de Oracle DB 12.1 Standard e Enterprise em execução no Azure em imagens de máquinas virtuais com base no Oracle Linux.  Para obter o melhor desempenho para cargas de trabalho de produção do Oracle DB no Azure, certifique-se de que corretamente redimensionar a imagem de VM e utilizar Managed Disks apoiadas pelo armazenamento Premium. Para obter instruções sobre como começar rapidamente um Oracle DB em execução no Azure com o Oracle publicado a imagem de VM [Experimente o passo a passo guia de introdução do Oracle DB](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opções de configuração de disco ligado

Discos ligados contam com o serviço de armazenamento de Blobs do Azure. Cada disco standard é capaz de um máximo teórico de aproximadamente 500 operações de entrada/saída por segundo (IOPS). A nossa oferta de disco premium é preferencial para cargas de trabalho de base de dados de elevado desempenho e pode alcançar até 5000 IOps por disco. Enquanto pode usar um único disco se de que atenda às suas necessidades de desempenho – pode melhorar o desempenho de IOPS eficaz se usar vários discos ligados, atravessando-os dados de base de dados e, em seguida, utilizar a gestão de armazenamento automática da Oracle (ASM). Ver [descrição geral de armazenamento automática do Oracle](https://www.oracle.com/technetwork/database/index-100339.html) para obter mais informações específicas do Oracle ASM. Para obter um exemplo de como instalar e configurar o Oracle ASM numa VM Linux do Azure - pode experimentar o [instalar e configurar o Oracle automatizada gestão de armazenamento](configure-oracle-asm.md) tutorial.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Cluster de aplicativo Real do Oracle (Oracle RAC)
Oracle RAC foi concebida para reduzir a falha de um único nó numa configuração de cluster de vários nós no local. Ele conta com duas tecnologias no local que não são nativas para ambientes de cloud pública de hiper escala: multicast de rede e disco partilhado. Se a sua solução de base de dados requer RAC Oracle no Azure, terá de 3º software de terceiros para permitir que essas tecnologias. Para obter mais informações sobre Oracle RAC, consulte a [página de solução de FlashGrid](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações de recuperação após desastre e de disponibilidade elevada
Quando utilizar bases de dados Oracle no Azure, são responsáveis por implementar uma solução de recuperação de desastre e de disponibilidade elevada para evitar qualquer período de inatividade. 

Elevada disponibilidade e recuperação após desastre do Oracle Database Enterprise Edition (sem contar com Oracle RAC) pode ser obtida no Azure utilizando [Data Guard, Active Directory Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), ou [Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate), com duas bases de dados em duas máquinas virtuais separadas. Ambas as máquinas virtuais devem estar na mesma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que consegue aceder entre si através do endereço IP privado persistente.  Além disso, é recomendável colocar as máquinas virtuais no mesmo conjunto de disponibilidade para permitir que o Azure para colocá-los em domínios de falha e domínios de atualização.  Se quiser ter a redundância geográfica - pode ter esses dois bancos de dados replicar entre duas regiões diferentes e ligue-se as duas instâncias com um Gateway de VPN.

Temos um tutorial "[implementar Oracle DataGuard no Azure](configure-oracle-dataguard.md)", que descreve o procedimento de configuração básica para a versão de avaliação isso no Azure.  

Com a proteção de dados Oracle, elevada disponibilidade pode ser obtida com uma base de dados primária numa máquina virtual, uma base de dados (em espera) secundária em outra máquina virtual e os replicação unidirecional configurar entre eles. O resultado é o acesso de leitura para a cópia da base de dados. Com o Oracle GoldenGate, pode configurar a replicação de bidirecionais entre duas bases de dados. Para saber como configurar uma solução de elevada disponibilidade para as bases de dados com estas ferramentas, veja [Active Directory Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) documentação no site da Oracle. Se precisar de acesso leitura-gravação para a cópia da base de dados, pode utilizar [Active Directory Oracle Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Temos um tutorial "[implementar Oracle GoldenGate no Azure](configure-oracle-golden-gate.md)", que descreve o procedimento de configuração básica para a versão de avaliação isso no Azure.

Apesar de terem uma solução HA e DR, criada no Azure, pretender certificar-se de que tem uma estratégia de cópia de segurança necessários para restaurar a base de dados.  Temos um tutorial [cópia de segurança e recuperar uma base de dados do Oracle](oracle-backup-recovery.md) explica como o procedimento básico para o estabelecimento de uma cópia de segurança consistente.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Imagens de máquina virtual do Oracle WebLogic Server
* **Clustering é suportado na edição Enterprise apenas.** Está licenciado para utilizar o WebLogic clustering apenas quando utilizar a Enterprise Edition do WebLogic Server. Não utilize o clustering com WebLogic Server Standard Edition.
* **Multicast de protocolo UDP não é suportada.** O Azure suporta UDP unicast, mas não utilizar multicast ou difusão. WebLogic Server é capaz de contar com capacidades do Azure UDP unicast. Para melhor os resultados da entidade confiadora no UDP unicast, recomendamos que o tamanho do cluster WebLogic seja mantida estático, ou ser mantidos com não mais do que 10 servidores gerenciados incluídos no cluster.
* **WebLogic Server espera que as portas públicas e privadas para ser o mesmo para o acesso de T3 (por exemplo, ao utilizar o Enterprise JavaBeans).** Considere um cenário de várias camado, onde uma aplicação de camada (EJB) do serviço está em execução num cluster de servidores de WebLogic consiste em duas ou mais VMs numa vNet com o nome **SLWLS**. O escalão de cliente é numa sub-rede diferente na mesma vNet, execução de um simple programa de Java tentando chamar EJB na camada de serviço. Porque é necessário para a camada de serviço de balanceamento de carga, um ponto de final com balanceamento de carga público tem de ser criada para as máquinas virtuais no cluster WebLogic Server. Se a porta privada que especificou for diferente da porta pública (por exemplo, 7006:7008), ocorre um erro, como o seguinte:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Isso é porque para qualquer acesso remoto do T3, WebLogic Server espera que a porta do Balanceador de carga e a porta do servidor WebLogic gerido para ser o mesmo. No caso anterior, o cliente está a aceder a porta 7006 (a porta do Balanceador de carga) e o servidor gerido está a escutar 7008 (a porta privada). Esta restrição é aplicável apenas para acesso T3, não HTTP.

   Para evitar este problema, utilize uma das seguintes soluções:

  * Utilize os mesmos números de porta privada e pública para os pontos finais com balanceamento de carga dedicados para o acesso do T3.
  * Inclua o seguinte parâmetro JVM ao iniciar o WebLogic Server:

         -Dweblogic.rjvm.enableprotocolswitch=true

Para obter informações relacionadas, consulte o artigo BDC **860340.1** em <https://support.oracle.com>.

* **Dinâmica clustering e limitações de balanceamento de carga.** Suponha que pretende utilizar um cluster dinâmico no WebLogic Server e expô-lo através de um único e público com balanceamento de carga ponto final no Azure. Isto pode ser efetuado desde que utilize um número de porta fixa para cada um dos servidores geridos (não dinamicamente atribuídos de um intervalo) e não se iniciam mais servidores geridos que existem o administrador está acompanhando as máquinas virtuais (ou seja, mais do que um servidores gerenciados por virtual m achine). Se a sua configuração resulta em mais servidores de WebLogic a ser iniciados do que as máquinas virtuais (ou seja, onde várias instâncias de WebLogic Server partilham a mesma máquina virtual), então, não é possível que mais de uma dessas instâncias de servidores de WebLogic para ligar a um número de porta designada – os outros nessa máquina virtual falharem.

   Se configurar o servidor de administração para atribuir automaticamente os números de porta únicos para os servidores gerenciados, em seguida, balanceamento de carga não é possível porque o Azure não suporta o mapeamento de uma única porta pública para várias portas privadas, como seria necessário para isso configuração.
* **Várias instâncias do Weblogic Server numa máquina virtual.** Dependendo das necessidades da sua implementação, pode considerar a opção de executar várias instâncias do WebLogic Server na mesma máquina virtual, se a máquina virtual é grande o suficiente. Por exemplo, numa máquina virtual média dimensão, que contém dois núcleos, poderia optar por executar duas instâncias do WebLogic Server. Observe no entanto que, recomendamos que evite introduzir pontos únicos de falha em sua arquitetura, o que seria o caso se utilizou apenas uma máquina virtual que está a executar várias instâncias do WebLogic Server. Utilizar, pelo menos, duas máquinas virtuais pode ser uma abordagem melhor, e cada uma dessas máquinas virtuais, em seguida, pode executar várias instâncias do WebLogic Server. Cada uma destas instâncias de WebLogic Server ainda pode ser parte do mesmo cluster. Observe, no entanto, atualmente não é possível utilizar o Azure para pontos finais de balanceamento de carga que são expostos por implementações deste tipo WebLogic Server na mesma máquina virtual, porque o Balanceador de carga do Azure requer os servidores com balanceamento de carga para ser distribuído entre exclusivo máquinas virtuais.

## <a name="oracle-jdk-virtual-machine-images"></a>Imagens de máquina virtual do Oracle JDK
* **JDK 6 e 7 atualizações mais recentes.** Embora, recomendamos que utilize a versão mais recente pública, suportada do Java (atualmente a Java 8), Azure também disponibiliza JDK 6 e 7 imagens. Isso se destina-se a aplicativos herdados que ainda não estão prontos para serem atualizados para o JDK 8. Embora as atualizações para imagens JDK anteriores poderão já não estar disponíveis ao público em geral, tendo em conta a parceria da Microsoft com a Oracle, o JDK 6 e 7 imagens fornecidas pelo Azure têm a finalidade de contêm uma atualização não pública mais recente que normalmente é oferecida por Oracle para apenas a um grupo de Oracle do suporte aos clientes. Novas versões das imagens JDK serão disponibilizadas ao longo do tempo com as versões atualizadas do JDK 6 e 7.

   O JDK disponível neste 6 JDK e 7 imagens e as máquinas virtuais e imagens derivadas deles, só pode ser utilizado no Azure.
* **JDK de 64 bits.** As imagens de máquina virtual do Oracle WebLogic Server e as imagens de máquina virtual Oracle JDK fornecidas pelo Azure contêm as versões de 64 bits do Windows Server e o JDK.

## <a name="next-steps"></a>Passos Seguintes
Tem agora uma descrição geral das soluções atuais do Oracle no Microsoft Azure. O próximo passo é implementar a sua primeira base de dados do Oracle no Azure.
- Experimente o [criar uma base de dados do Oracle no Azure](oracle-database-quick-create.md) tutorial para começar a utilizar.
