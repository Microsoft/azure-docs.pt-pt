---
title: Configurar a recuperação após desastre fo uma aplicação web com base no IIS com o Azure Site Recovery de várias camadas | Documentos da Microsoft
description: Saiba como replicar máquinas virtuais IIS web farm com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 66b9342f1a67c4c9d35fda447a297cc64d048c1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480304"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Configurar a recuperação após desastre para uma aplicação web baseada no IIS de várias camadas

Software de aplicativos é o mecanismo de produtividade de negócios numa organização. Vários aplicativos web podem servir finalidades diferentes numa organização. Alguns aplicativos, como as aplicações utilizadas para processamento de folha de pagamento, aplicativos financeiros e Web sites do lado do cliente, podem ser fundamentais para uma organização. Para evitar a perda de produtividade, é importante para a organização ter esses aplicativos continuamente em funcionamento. Mais importante, ter esses aplicativos consistentemente disponíveis pode ajudar a evitar danos à marca ou imagem da organização.

Aplicações web críticas são normalmente configuradas como aplicações de várias camadas: web, base de dados e aplicações estão em diferentes camadas. Para além de que está a ser-se distribuídas por vários escalões, os aplicativos também podem usar vários servidores em cada camada para o tráfego de balanceamento de carga. Além disso, os mapeamentos entre as várias camadas e no servidor web podem ser baseados em endereços IP estáticos. Na ativação pós-falha, alguns desses mapeamentos têm de ser atualizado, especialmente se vários Web sites estão configurados no servidor web. Se os aplicativos web utilizam SSL, tem de atualizar os enlaces de certificado.

Métodos de recuperação tradicionais que não são baseados em replicação envolvem a cópia de segurança vários arquivos de configuração, definições de registo, ligações, componentes personalizados (COM ou .NET), conteúdo e certificados. Arquivos são recuperados através de um conjunto de passos manuais. Os métodos de recuperação tradicionais de cópia de segurança e recuperação de ficheiros manualmente são complicado, propenso a erros e não escalável. Por exemplo, pode facilmente se esqueça de fazer cópias de segurança de certificados. Após a ativação pós-falha, fica apenas com outra opção a não mas comprar novos certificados para o servidor.

Uma solução de recuperação após desastre boa suporta a recuperação de modelagem planos para arquiteturas de aplicativos complexos. Deve também conseguir adicionar etapas personalizadas para o plano de recuperação para lidar com mapeamentos de aplicação entre camadas. Se houver um desastre, mapeamentos de aplicação fornecem uma solução de clique único, a captura de-se de que o ajuda a levar a um RTO inferior.

Este artigo descreve como proteger uma aplicação web que se baseia nos serviços de informação Internet (IIS), utilizando [do Azure Site Recovery](site-recovery-overview.md). O artigo abrange as melhores práticas para replicar uma aplicação web de três camadas, com base no IIS para o Azure, como fazer um teste de recuperação após desastre e como fazer a ativação pós-falha a aplicação no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que sabe como fazer as seguintes tarefas:

* [Replicar uma máquina virtual para o Azure](vmware-azure-tutorial.md)
* [Criar uma rede de recuperação](site-recovery-network-design.md)
* [Fazer uma ativação pós-falha de teste para o Azure](site-recovery-test-failover-to-azure.md)
* [Fazer uma ativação pós-falha para o Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicação do SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implementação
Uma aplicação web com base no IIS, normalmente, segue-se um dos seguintes padrões de implementação:

**Padrão de implementação 1**

Um farm da web com base no IIS com a aplicação de encaminhamento de pedidos (ARR), um servidor IIS e SQL Server.

![Diagrama de um farm de servidores web com base no IIS que tem três camadas](./media/site-recovery-iis/deployment-pattern1.png)

**Padrão de implementação 2**

Um farm da web com base no IIS com o ARR, um servidor IIS, um servidor de aplicações e do SQL Server.

![Diagrama de um farm da web com base no IIS que tem quatro escalões](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Os exemplos neste artigo, vamos utilizar máquinas virtuais VMware com o IIS 7.5 no Windows Server 2012 R2 Enterprise. Uma vez que a replicação do Site Recovery não é específico do aplicativo, as recomendações neste artigo devem aplicam-se nos cenários listados na tabela a seguir e para as diferentes versões do IIS.

### <a name="source-and-target"></a>Origem e destino

Cenário | Para um site secundário | Para o Azure
--- | --- | ---
Hyper-V | Sim | Sim
VMware | Sim | Sim
Servidor físico | Não | Sim
Azure|N/D|Sim

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para começar a replicar todas as IIS web farm máquinas virtuais para o Azure, siga as orientações no [ativação pós-falha de teste para o Azure no Site Recovery](site-recovery-test-failover-to-azure.md).

Se estiver a utilizar um endereço IP estático, pode especificar o endereço IP que pretende que a máquina virtual a tomar. Para definir o endereço IP, aceda a **computação e as definições de rede** > **IP de destino**.

![Captura de ecrã que mostra como definir o IP de destino no painel de computação de recuperação de Site e da rede](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação suporta a sequenciação de vários escalões num aplicativo de várias camado durante uma ativação pós-falha. Sequenciamento ajuda a manter a consistência de aplicação. Quando cria um plano de recuperação para uma aplicação web de vários escalões, complete os passos descritos na [criar um plano de recuperação utilizando a recuperação de Site](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais para grupos de ativação pós-falha
Um aplicativo web do IIS de várias camado típico consiste nos seguintes componentes:
* Uma camada de base de dados que tenha máquinas virtuais do SQL.
* A camada web, que consiste num servidor IIS e uma camada de aplicação. 

Adicione máquinas virtuais para diferentes grupos com base no escalão:

1. Crie um plano de recuperação. Adicione as máquinas de virtuais de camada de base de dados de grupo 1. Isso garante que as máquinas de virtuais de camada de base de dados são encerrar a última e colocadas primeiro.
1. Adicione as máquinas de virtuais de camada de aplicativo de grupo 2. Isto garante que as máquinas de virtuais de camada de aplicativo são colocadas depois da camada de base de dados tem sido recuperada.
1. Adicione as máquinas de virtuais de camada web no grupo de 3. Isto garante que as máquinas de virtuais de camada web são colocadas depois de camada de aplicativos foram colocada.
1. Adicione máquinas de virtuais de balanceamento de carga em 4 de grupo. Isto garante que máquinas de virtuais de balanceamento de carga são colocadas depois da camada web foram colocada.

Para obter mais informações, consulte [personalizar o plano de recuperação](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Adicionar um script para o plano de recuperação
Para o farm de web do IIS funcione corretamente, poderá ter de fazer algumas operações em pós-ativação pós-falha de máquinas virtuais do Azure ou durante uma ativação pós-falha de teste. Pode automatizar algumas operações após a ativação pós-falha. Por exemplo, pode atualizar a entrada DNS, alterar um enlace de site ou alterar uma cadeia de ligação, adicionando scripts correspondentes para o plano de recuperação. [Adicionar um script VMM para um plano de recuperação](site-recovery-how-to-add-vmmscript.md) descreve como configurar tarefas automatizadas usando um script.

#### <a name="dns-update"></a>Atualização de DNS
Se o DNS está configurado para atualizar o DNS dinâmico, as máquinas virtuais Atualize, normalmente, o DNS com o novo endereço IP, quando são iniciados. Se pretender adicionar um passo explícito para atualizar o DNS com os novos endereços IP das máquinas virtuais, adicione uma [script para atualizar o IP no DNS](https://aka.ms/asr-dns-update) como uma ação após a ativação pós-falha em grupos de plano de recuperação.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Cadeia de ligação na Web. config de uma aplicação
A cadeia de ligação Especifica a base de dados que o Web site comunica com. Se a cadeia de ligação tem o nome da máquina virtual da base de dados, não existem passos adicionais são necessária pós-falha. A aplicação pode automaticamente comunicar com a base de dados. Além disso, se o endereço IP para a máquina virtual de base de dados é retido, não ser necessário atualizar a cadeia de ligação. 

Se a cadeia de ligação refere-se para a máquina virtual de base de dados utilizando um endereço IP, ele precisa ser atualizada pós-falha. Por exemplo, os seguintes pontos de cadeia de ligação à base de dados com o IP endereços 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Para atualizar a cadeia de ligação na camada web, adicione uma [script de atualização de ligação de IIS](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) após 3 de grupo no plano de recuperação.

#### <a name="site-bindings-for-the-application"></a>Enlaces de site para a aplicação
Todos os sites consiste em informações de enlace. As informações de enlace incluem o tipo de ligação, o endereço IP em que o servidor IIS escuta para os pedidos para o site, o número de porta e os nomes de anfitrião para o site. Durante a ativação pós-falha, poderá ter de atualizar essas associações, se houver uma alteração no endereço IP que está associada com eles.

> [!NOTE]
>
> Se definir o enlace de site como **todos os não atribuídos**, não precisa de atualizar este enlace pós-ativação pós-falha. Além disso, se o endereço IP associado um site não for alterada de pós-falha, não precisa de atualizar o enlace de site. (O período de retenção do endereço IP depende da arquitetura de rede e sub-redes atribuídas aos sites primário e de recuperação. A atualizá-los poderá não ser viável para a sua organização.)

![Captura de ecrã que mostra a configuração o enlace SSL](./media/site-recovery-iis/sslbinding.png)

Se tiver associado o endereço IP um site, atualize todos os enlaces de site com o novo endereço IP. Para alterar os enlaces de site, adicione uma [script de atualização do escalão de web IIS](https://aka.ms/asr-web-tier-update-runbook-classic) após 3 de grupo no plano de recuperação.

#### <a name="update-the-load-balancer-ip-address"></a>Atualizar o endereço IP do Balanceador de carga
Se tiver uma máquina virtual do ARR, para atualizar o endereço IP, adicionar um [script de ativação pós-falha do IIS ARR](https://aka.ms/asr-iis-arrtier-failover-script-classic) após 4 de grupo.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>Enlace de certificado SSL para uma ligação HTTPS
Um Web site pode ter um certificado SSL associado que ajuda a garantir uma comunicação segura entre o servidor web e o navegador do usuário. Se o Web site tiver uma ligação HTTPS e também tem um enlace de site HTTPS associado, para o endereço IP do servidor do IIS com um enlace de certificado SSL, tem de adicionar um novo enlace de site para o certificado com o endereço IP do após a máquina virtual do IIS-ativação pós-falha.

O certificado SSL pode ser emitido em relação a esses componentes:

* O nome de domínio completamente qualificado do Web site.
* O nome do servidor.
* Um certificado de caráter universal para o nome de domínio.  
* Um endereço IP. Se o certificado SSL foi emitido em comparação ao endereço IP do servidor IIS, outro certificado SSL tem de ser emitido em comparação ao endereço IP do servidor IIS no site do Azure. Um enlace de SSL adicional para este certificado tem de ser criada. Por este motivo, recomendamos que não utilize um certificado SSL emitido em comparação ao endereço IP. Esta opção é menos amplamente usada e em breve será preterida em conformidade com as novas alterações de fórum de autoridade/browser de certificado.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Atualizar a dependência entre a camada web e a camada de aplicação
Se tiver uma dependência de específico do aplicativo com base no endereço IP das máquinas virtuais, tem de atualizar este pós-falha dependência.

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. No portal do Azure, selecione o Cofre de serviços de recuperação.
2. Selecione o plano de recuperação que criou para a web farm do IIS.
3. Selecione **Ativação Pós-falha de Teste**.
4. Para iniciar o processo de ativação pós-falha de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5. Quando o ambiente secundário está ativo, pode realizar as validações.
6. Quando validações forem concluídas, para limpar o ambiente de ativação pós-falha de teste, selecione **validações concluir**.

Para obter mais informações, consulte [ativação pós-falha de teste para o Azure no Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1. No portal do Azure, selecione o Cofre de serviços de recuperação.
1. Selecione o plano de recuperação que criou para a web farm do IIS.
1. Selecione **Ativação pós-falha**.
1. Para iniciar o processo de ativação pós-falha, selecione o ponto de recuperação.

Para obter mais informações, consulte [ativação pós-falha no Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [replicar outros aplicativos](site-recovery-workload.md) utilizando a recuperação de Site.
