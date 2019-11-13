---
title: Configurar a recuperação de desastre para um aplicativo Web do IIS usando Azure Site Recovery
description: Saiba como replicar máquinas virtuais do IIS web farm usando Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 513a0f28fc03cbf24e35112245c9756d5ce00783
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954672"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Configurar a recuperação de desastre para um aplicativo Web baseado em IIS de várias camadas

O software de aplicativos é o mecanismo de produtividade dos negócios em uma organização. Vários aplicativos Web podem atender a finalidades diferentes em uma organização. Alguns aplicativos, como aplicativos usados para processamento de folha de pagamento, aplicativos financeiros e sites voltados para o cliente, podem ser essenciais para uma organização. Para evitar a perda de produtividade, é importante que a organização tenha esses aplicativos continuamente em funcionamento. O mais importante é que ter esses aplicativos consistentemente disponíveis pode ajudar a evitar danos à marca ou à imagem da organização.

Aplicativos Web críticos normalmente são configurados como aplicativos de várias camadas: a Web, o banco de dados e o aplicativo estão em camadas diferentes. Além de serem distribuídos em várias camadas, os aplicativos também podem usar vários servidores em cada camada para balancear a carga do tráfego. Além disso, os mapeamentos entre várias camadas e no servidor Web podem ser baseados em endereços IP estáticos. No failover, alguns desses mapeamentos precisam ser atualizados, especialmente se vários sites estiverem configurados no servidor Web. Se os aplicativos Web usarem SSL, você deverá atualizar as associações de certificado.

Os métodos de recuperação tradicionais que não são baseados na replicação envolvem o backup de vários arquivos de configuração, configurações do registro, associações, componentes personalizados (COM ou .NET), conteúdo e certificados. Os arquivos são recuperados por meio de um conjunto de etapas manuais. Os métodos de recuperação tradicionais de backup e recuperação manual de arquivos são complicados, sujeitos a erros e não escalonáveis. Por exemplo, você pode se esquecer facilmente de fazer backup de certificados. Após o failover, você não tem nenhuma opção, mas sim comprar novos certificados para o servidor.

Uma boa solução de recuperação de desastre dá suporte à modelagem de planos de recuperação para arquiteturas de aplicativos complexas. Você também deve ser capaz de adicionar etapas personalizadas ao plano de recuperação para manipular mapeamentos de aplicativos entre camadas. Se houver um desastre, os mapeamentos de aplicativos fornecerão uma solução com um único clique, que ajuda a levar a um RTO mais baixo.

Este artigo descreve como proteger um aplicativo Web baseado em Serviços de Informações da Internet (IIS) usando [Azure site Recovery](site-recovery-overview.md). O artigo aborda as práticas recomendadas para replicar um aplicativo Web baseado no IIS de três camadas para o Azure, como fazer uma análise de recuperação de desastre e como executar o failover do aplicativo para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você sabe como realizar as seguintes tarefas:

* [Replicar uma máquina virtual no Azure](vmware-azure-tutorial.md)
* [Criar uma rede de recuperação](site-recovery-network-design.md)
* [Fazer um failover de teste para o Azure](site-recovery-test-failover-to-azure.md)
* [Fazer um failover para o Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicação do SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implantação
Um aplicativo Web baseado no IIS geralmente segue um dos seguintes padrões de implantação:

**Padrão de implantação 1**

Um web farm baseado em IIS com Application Request Routing (ARR), um servidor IIS e SQL Server.

![Diagrama de um web farm baseado em IIS que tem três camadas](./media/site-recovery-iis/deployment-pattern1.png)

**Padrão de implantação 2**

Um web farm baseado no IIS com ARR, um servidor IIS, um servidor de aplicativos e SQL Server.

![Diagrama de um web farm baseado em IIS que tem quatro camadas](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para os exemplos neste artigo, usamos máquinas virtuais VMware com o IIS 7,5 no Windows Server 2012 R2 Enterprise. Como Site Recovery replicação não é específica do aplicativo, espera-se que as recomendações neste artigo sejam aplicadas nos cenários listados na tabela a seguir e para versões diferentes do IIS.

### <a name="source-and-target"></a>Origem e destino

Cenário | Para um site secundário | Para o Azure
--- | --- | ---
Hyper-V | Sim | Sim
VMware | Sim | Sim
Servidor físico | Não | Sim
Azure|ND|Sim

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para iniciar a replicação de todas as máquinas virtuais do IIS web farm para o Azure, siga as orientações em [failover de teste para o Azure em site Recovery](site-recovery-test-failover-to-azure.md).

Se você estiver usando um endereço IP estático, poderá especificar o endereço IP que você deseja que a máquina virtual execute. Para definir o endereço IP, vá para **configurações de computação e rede** > **IP de destino**.

![Captura de tela que mostra como definir o IP de destino no painel Site Recovery computação e rede](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação dá suporte ao sequenciamento de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Ao criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [criar um plano de recuperação usando site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais a grupos de failover
Um aplicativo Web do IIS de várias camadas típico consiste nos seguintes componentes:
* Uma camada de banco de dados que tem máquinas virtuais do SQL.
* A camada da Web, que consiste em um servidor IIS e uma camada de aplicativo. 

Adicione máquinas virtuais a grupos diferentes com base na camada:

1. Crie um plano de recuperação. Adicione as máquinas virtuais da camada de banco de dados no grupo 1. Isso garante que as máquinas virtuais da camada de banco de dados sejam desligadas por último e ativadas primeiro.
1. Adicione as máquinas virtuais da camada de aplicativo no grupo 2. Isso garante que as máquinas virtuais da camada de aplicativo sejam ativadas depois que a camada do banco de dados for colocada.
1. Adicione as máquinas virtuais da camada da Web no grupo 3. Isso garante que as máquinas virtuais da camada da Web sejam ativadas depois que a camada de aplicativo for colocada.
1. Adicione máquinas virtuais de balanceamento de carga no grupo 4. Isso garante que as máquinas virtuais de balanceamento de carga sejam ativadas depois que a camada da Web for colocada.

Para obter mais informações, consulte [Personalizar o plano de recuperação](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Adicionar um script ao plano de recuperação
Para que o web farm do IIS funcione corretamente, talvez seja necessário realizar algumas operações nas máquinas virtuais do Azure após o failover ou durante um failover de teste. Você pode automatizar algumas operações pós-failover. Por exemplo, você pode atualizar a entrada DNS, alterar uma associação de site ou alterar uma cadeia de conexão adicionando scripts correspondentes ao plano de recuperação. [Adicionar um script do VMM a um plano de recuperação](site-recovery-how-to-add-vmmscript.md) descreve como configurar tarefas automatizadas usando um script.

#### <a name="dns-update"></a>Atualização de DNS
Se o DNS estiver configurado para atualização dinâmica de DNS, as máquinas virtuais geralmente atualizarão o DNS com o novo endereço IP quando iniciarem. Se você quiser adicionar uma etapa explícita para atualizar o DNS com os novos endereços IP das máquinas virtuais, adicione um [script para atualizar o IP no DNS](https://aka.ms/asr-dns-update) como uma ação de pós-failover nos grupos de planos de recuperação.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Cadeia de conexão no Web. config de um aplicativo
A cadeia de conexão especifica o banco de dados com o qual o site se comunica. Se a cadeia de conexão transportar o nome da máquina virtual do banco de dados, não serão necessárias mais etapas após o failover. O aplicativo pode se comunicar automaticamente com o banco de dados. Além disso, se o endereço IP da máquina virtual do banco de dados for mantido, ele não precisará atualizar a cadeia de conexão. 

Se a cadeia de conexão se referir à máquina virtual do banco de dados usando um endereço IP, ela precisará ser atualizada após o failover. Por exemplo, a cadeia de conexão a seguir aponta para o banco de dados com o endereço IP 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Para atualizar a cadeia de conexão na camada da Web, adicione um [script de atualização de conexão do IIS](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) após o grupo 3 no plano de recuperação.

#### <a name="site-bindings-for-the-application"></a>Associações de site para o aplicativo
Cada site consiste em informações de associação. As informações de associação incluem o tipo de associação, o endereço IP no qual o servidor IIS escuta as solicitações do site, o número da porta e os nomes de host para o site. Durante o failover, talvez seja necessário atualizar essas associações se houver uma alteração no endereço IP associado a elas.

> [!NOTE]
>
> Se você definir a associação de site a **todos os não atribuídos**, não será necessário atualizar essa associação após o failover. Além disso, se o endereço IP associado a um site não for alterado após o failover, você não precisará atualizar a associação do site. (A retenção do endereço IP depende da arquitetura da rede e das sub-redes atribuídas aos sites primário e de recuperação. Atualizá-los pode não ser viável para sua organização.)

![Captura de tela que mostra a configuração da Associação SSL](./media/site-recovery-iis/sslbinding.png)

Se você tiver associado o endereço IP a um site, atualize todas as associações de site com o novo endereço IP. Para alterar as associações do site, adicione um [script de atualização da camada da Web do IIS](https://aka.ms/asr-web-tier-update-runbook-classic) após o grupo 3 no plano de recuperação.

#### <a name="update-the-load-balancer-ip-address"></a>Atualizar o endereço IP do balanceador de carga
Se você tiver uma máquina virtual ARR, para atualizar o endereço IP, adicione um [script de failover arr do IIS](https://aka.ms/asr-iis-arrtier-failover-script-classic) após o grupo 4.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>Associação de certificado SSL para uma conexão HTTPS
Um site pode ter um certificado SSL associado que ajuda a garantir uma comunicação segura entre o servidor Web e o navegador do usuário. Se o site tiver uma conexão HTTPS e também tiver uma associação de site HTTPS associada ao endereço IP do servidor IIS com uma associação de certificado SSL, você deverá adicionar uma nova associação de site para o certificado com o endereço IP da máquina virtual do IIS após o failover.

O certificado SSL pode ser emitido em relação a estes componentes:

* O nome de domínio totalmente qualificado do site.
* O nome do servidor.
* Um certificado curinga para o nome de domínio.  
* Um endereço IP. Se o certificado SSL for emitido em relação ao endereço IP do servidor IIS, outro certificado SSL precisará ser emitido em relação ao endereço IP do servidor IIS no site do Azure. É necessário criar uma associação SSL adicional para esse certificado. Por isso, é recomendável não usar um certificado SSL emitido no endereço IP. Essa opção é menos usada e, em breve, será preterida de acordo com as novas alterações de fórum de autoridade de certificação/navegador.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Atualizar a dependência entre a camada da Web e a camada de aplicativo
Se você tiver uma dependência específica do aplicativo com base no endereço IP das máquinas virtuais, deverá atualizar essa dependência após o failover.

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. Na portal do Azure, selecione o cofre dos serviços de recuperação.
2. Selecione o plano de recuperação que você criou para o web farm do IIS.
3. Selecione **Ativação Pós-falha de Teste**.
4. Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5. Quando o ambiente secundário estiver ativo, você poderá executar validações.
6. Quando as validações forem concluídas, para limpar o ambiente de failover de teste, selecione **validações concluídas**.

Para obter mais informações, consulte [failover de teste para o Azure no site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1. Na portal do Azure, selecione o cofre dos serviços de recuperação.
1. Selecione o plano de recuperação que você criou para o web farm do IIS.
1. Selecione **Ativação pós-falha**.
1. Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [failover em site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como [replicar outros aplicativos](site-recovery-workload.md) usando site Recovery.
