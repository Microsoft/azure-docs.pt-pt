---
title: Avaliar servidores utilizando dados de servidores importados com avaliação do servidor Azure Migrate
description: Descreve como avaliar os servidores no local para migração para Azure com a Azure Migrate Server Assessment usando dados importados.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 98675b0f986ecb78ff122ed052a01d521aac1f6f
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114215"
---
# <a name="assess-servers-by-using-imported-data"></a>Avaliar servidores utilizando dados importados

Este artigo explica como avaliar os servidores no local com a ferramenta [Azure Migrate: Server Assessment,](migrate-services-overview.md#azure-migrate-server-assessment-tool) importando metadados de servidores em formato de valores separados por vírgula (CSV). Este método de avaliação não requer que você crie o aparelho Azure Migrate para criar uma avaliação. É útil se:

- Pretende-se criar uma avaliação rápida e inicial antes de colocar o aparelho.
- Não pode colocar o aparelho Azure Migrate na sua organização.
- Não é possível partilhar credenciais que permitam o acesso a servidores no local.
- As restrições de segurança impedem-no de recolher e enviar dados recolhidos pelo aparelho para a Azure. Pode controlar os dados que partilha num ficheiro importado. Além disso, grande parte dos dados (por exemplo, fornecer endereços IP) é opcional.

## <a name="before-you-start"></a>Antes de começar

Esteja atento a estes pontos:

- Pode adicionar até um máximo de 20.000 servidores num único ficheiro CSV.
- Pode adicionar até 20.000 servidores num projeto Azure Migrate utilizando o CSV.
- Pode enviar informações do servidor para a Avaliação do Servidor várias vezes utilizando o CSV.
- Recolher informações sobre aplicações é útil na avaliação do seu ambiente no local para a migração. No entanto, a Avaliação do Servidor não realiza atualmente uma avaliação ao nível da aplicação ou tem em conta as aplicações ao criar uma avaliação.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Crie um projeto Azure Migrate.
> * Preencha um ficheiro CSV com informações do servidor.
> * Importe o ficheiro para adicionar informações do servidor na Avaliação do Servidor.
> * Criar e rever uma avaliação.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário, para que possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os guias de como fazer.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="set-azure-permissions-for-azure-migrate"></a>Definir permissões Azure para Azure Migrate

A sua conta Azure precisa de permissões para criar um projeto Azure Migrate.

1. No portal Azure, abra a subscrição e selecione **Access control (IAM)**.
2. No **Acesso ao Cheque,** encontre a conta relevante e, em seguida, selecione-a para ver permissões.
3. Certifique-se de que tem permissões **de Colaborador** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não é o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.

## <a name="set-up-an-azure-migrate-project"></a>Criar um projeto Azure Migrate

Para criar um novo projeto Azure Migrate:

1. No portal Azure, em **Todos os serviços,** procure **a Azure Migrate.**
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Visão Geral**, em **Discover, avaliar e migrar servidores,** selecione **Avaliar e migrar servidores**.

    ![Descubra e avalie servidores](./media/tutorial-assess-import/assess-migrate.png)

4. Para **começar,** **selecione Adicionar ferramentas.**
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
6. Nos **detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Para obter mais informações:

    - Reveja as regiões suportadas em [clouds públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e do [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-import/migrate-project.png)

7. Selecione **Seguinte**.
8. Na **ferramenta de avaliação Select**, selecione **Azure Migrate: Avaliação do servidor**Em  >  **seguida**.

    ![Criar uma avaliação de Azure Migrate](./media/tutorial-assess-import/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Rever + adicionar ferramentas,** rever as definições e, em seguida, selecionar **Ferramentas Adicionar**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Em seguida, será levado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="prepare-the-csv"></a>Preparar o CSV

Descarregue o modelo CSV e adicione informações do servidor.

### <a name="download-the-template"></a>Transferir o modelo

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, select **Discover**.
2. Nas **máquinas Discover**, selecione **Import using CSV**.
3. Selecione **Baixar** para descarregar o modelo CSV. Em alternativa, pode [descarregá-lo diretamente.](https://go.microsoft.com/fwlink/?linkid=2109031)

    ![Baixar o modelo CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Adicionar informações do servidor

Recolha os dados do servidor e adicione-os ao ficheiro CSV.

- Para recolher dados, pode exportá-lo a partir de ferramentas que utiliza para a gestão de servidores no local, como vMware vSphere ou a sua base de dados de gestão de configuração (CMDB).
- Para rever os dados da amostra, descarregue o nosso [ficheiro de exemplo.](https://go.microsoft.com/fwlink/?linkid=2108405)

A tabela seguinte resume os campos de ficheiros a preencher:

**Nome do campo** | **Obrigatório** | **Detalhes**
--- | --- | ---
**Nome do servidor** | Sim | Recomendamos especificar o nome de domínio totalmente qualificado (FQDN).
**Endereço IP** | Não | Endereço do servidor.
**Núcleos** | Sim | Número de núcleos de processador atribuídos ao servidor.
**Memória** | Sim | RAM total, em MB, alocado ao servidor.
**Nome do SO** | Sim | Sistema operativo do servidor. <br/> Os nomes do sistema operativo que correspondam ou contenham os nomes [desta](#supported-operating-system-names) lista são reconhecidos pela avaliação.
**Versão do SO** | Não | Versão do sistema operativo do servidor.
**Arquitetura de OS** | Não | Arquitetura de SERVIDOR OS <br/> Valores válidos são: x64, x86, amd64, 32-bit ou 64-bit
**Número de discos** | Não | Não é necessário se forem fornecidos detalhes individuais do disco.
**Disco 1 tamanho**  | Não | Tamanho máximo do disco, em GB.<br/>Pode adicionar detalhes para mais discos [adicionando colunas](#add-multiple-disks) no modelo. Pode somar até oito discos.
**Disk 1 ler ops** | Não | Operações de leitura de disco por segundo.
**Disk 1 escrever ops** | Não | Operações de escrita de disco por segundo.
**Produção de leitura de disco 1** | Não | Dados lidos a partir do disco por segundo, em MB por segundo.
**Saída de escrita do disco 1** | Não | Dados escritos para disco por segundo, em MB por segundo.
**Percentagem de utilização do CPU** | Não | Percentagem de CPU usado.
**Percentagem de utilização da memória** | Não | Percentagem de RAM usado.
**Total de discos lêem ops** | Não | Operações de leitura de discos por segundo.
**Os discos totais escrevem ops** | Não | Operações de gravação de discos por segundo.
**Total de discos lêem produção** | Não | Dados lidos a partir do disco, em MB por segundo.
**Total de discos escrevem produção** | Não | Dados escritos para o disco, em MB por segundo.
**Rede Em produção** | Não | Dados recebidos pelo servidor, em MB por segundo.
**Produção de Rede Para Fora** | Não | Dados transmitidos pelo servidor, em MB por segundo.
**Tipo de firmware** | Não | Firmware do servidor. Os valores podem ser "BIOS" ou "UEFI".
**Endereço MAC**| Não | Endereço MAC do servidor.


### <a name="add-operating-systems"></a>Adicionar sistemas operativos

A avaliação reconhece nomes específicos do sistema operativo. Qualquer nome que especifique deve corresponder exatamente a uma das cordas da lista de [nomes suportados](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Adicionar vários discos

O modelo fornece campos predefinidos para o primeiro disco. Pode adicionar colunas semelhantes para até oito discos.

Por exemplo, para especificar todos os campos para um segundo disco, adicione estas colunas:

- Disco 2 tamanho
- Disk 2 ler ops
- Disk 2 escrever ops
- Produção de leitura de disco 2
- Produção de escrita de disco 2


## <a name="import-the-server-information"></a>Importar a informação do servidor

Depois de adicionar informações ao modelo CSV, importe os servidores na Avaliação do Servidor.

1. Em Azure Migrate, em **máquinas Discover,** vá para o modelo completo.
2. Selecione **Importar**.
3. O estado de importação é indicado.
    - Se os avisos aparecerem no estado, pode corrigi-los ou continuar sem os abordar.
    - Para melhorar a precisão da avaliação, melhore as informações do servidor como sugerido nos avisos.
    - Para visualizar e corrigir avisos, **selecione Baixar os dados de aviso . CSV**. Esta operação descarrega o CSV com avisos incluídos. Reveja os avisos e corrija os problemas conforme necessário.
    - Se surgirem erros no estado de modo a que o estado de importação **seja falhado,** deve corrigir esses erros antes de poder continuar com a importação:
        1. Descarregue o CSV, que agora inclui detalhes de erro.
        1. Reveja e aborde os erros necessários. 
        1. Faça o upload do ficheiro modificado novamente.
4. Quando o estado de importação estiver **concluído,** a informação do servidor foi importada.

## <a name="update-server-information"></a>Atualizar informações do servidor

Pode atualizar as informações para um servidor importando novamente os dados do servidor com o mesmo **nome de Servidor**. Não é possível modificar o campo **de nomes do Servidor.** Atualmente, a eliminação de servidores não é suportada.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Para verificar se os servidores aparecem no portal Azure após a descoberta:

1. Abra o painel Azure Migrate.
2. No **Azure Migrate - Servidores**  >  **Azure Migrate:** Página de Avaliação do servidor, selecione o ícone que exibe a contagem para **servidores descobertos**.
3. Selecione o **separador Baseado em Importação.**

## <a name="set-up-and-run-an-assessment"></a>Configurar e executar uma avaliação

Pode criar dois tipos de avaliações utilizando a Avaliação do Servidor.


**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais Azure. <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md)no local, [VMs hiper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para Azure utilizando este tipo de avaliação. (concepts-assessment-calculation.md)
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md) no local para migração para Azure VMware Solution (AVS) utilizando este tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

### <a name="sizing-criteria"></a>Critérios de dimensionamento

A Avaliação do Servidor fornece duas opções de critérios de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações que fazem recomendações com base em dados de desempenho recolhidos | **Avaliação Azure VM**: A recomendação do tamanho de VM baseia-se em dados de cpu e utilização da memória.<br/><br/> A recomendação do tipo de disco (hdd/SSD padrão ou discos geridos por prémios) baseia-se no IOPS e na produção dos discos no local.<br/><br/> **Avaliação da Solução Azure VMware (AVS):** A recomendação dos nós AVS baseia-se em dados de CPU e utilização da memória.
**As-is in-ins** | Avaliações que não usam dados de desempenho para fazer recomendações. | **Avaliação Azure VM**: A recomendação do tamanho de VM baseia-se no tamanho VM no local<br/><br> O tipo de disco recomendado baseia-se no que seleciona na definição do tipo de armazenamento para a avaliação.<br/><br/> **Avaliação da Solução Azure VMware (AVS):** A recomendação dos nós AVS baseia-se no tamanho VM no local.


Para fazer uma avaliação:

1. Reveja as [melhores práticas](best-practices-assessment.md) para criar avaliações.
2. No separador **Servidores,** no **Azure Migrate: Telha de Avaliação** do Servidor, selecione **Avaliar**.

    ![Avaliar](./media/tutorial-assess-physical/assess.png)

3. Nos **servidores avaliação**, especifique o nome de avaliação e selecione o tipo **de avaliação** como *Azure VM* se pretender realizar avaliações Azure VM ou *Azure VMware Solution (AVS)* se pretender realizar avaliações AVS.

    ![Avaliação Básicos](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Na **fonte Discovery**, selecione **Machines adicionados através da importação para Azure Migrate**.

5. Selecione **Ver tudo** para rever as propriedades da avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-physical/view-all.png)

6. Clique **ao lado** de **Selecionar máquinas para avaliar**. Em **Select ou criar um grupo**, selecione Create **New**, e especifique um nome de grupo. Um grupo reúne um ou mais VMs para avaliação.
7. No **Adicionar máquinas ao grupo,** selecione servidores para adicionar ao grupo.
8. Clique **ao lado** do Review + crie **avaliação** para rever os detalhes da avaliação.
9. Clique **em Criar avaliação** para criar o grupo e, em seguida, executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-physical/assessment-create.png)

9. Após a avaliação ser criada, veja-a nos **Servidores**  >  **Azure Migrate: Avaliações de Avaliação do Servidor**  >  **Assessments**.
10. Selecione **a avaliação de exportação** para descarregá-lo como um ficheiro Microsoft Excel.

Para saber mais detalhes sobre a avaliação **da Solução VMware Azure VMware (AVS),** consulte [aqui](how-to-create-azure-vmware-solution-assessment.md). 

## <a name="review-an-azure-vm-assessment"></a>Reveja uma avaliação de Azure VM

Uma avaliação de Azure VM descreve:

- **Prontidão azul**: Se os servidores são adequados para a migração para Azure.
- **Estimativa mensal dos custos**: Estimativa mensal dos custos de cálculo e armazenamento para a execução dos servidores em Azure.
- **Estimativa mensal dos custos de armazenamento**: Custos estimados para o armazenamento do disco após a migração.

### <a name="view-an-assessment"></a>Ver uma avaliação

1. Nos **objetivos de migração**  >  **Servidores**, selecione **Avaliações** em **Azure Migrate: Avaliação do servidor**.
2. Em **Avaliações,** selecione uma avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Rever prontidão Azure

1. Em **Azure,** determine se os servidores estão prontos para a migração para Azure.
2. Reveja o estado:
    - **Pronto para Azure**: A azure Migrate recomenda uma estimativa de tamanho e custo de VM para VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e sugeriu a reparação.
    - **Não está pronto para o Azure:** Mostra problemas e sugeriu a reparação.
    - **Prontidão desconhecida**: A Azure Migrate não pode avaliar a prontidão, devido a problemas de disponibilidade de dados.

3. Selecione um estado **de prontidão Azure.** Pode ver os detalhes de prontidão do servidor e perfurar para ver detalhes do servidor, incluindo configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Rever detalhes de custos

Esta visão mostra o custo estimado de cálculo e armazenamento de VMs em execução em Azure. Pode:

- Reveja os custos mensais de computação e armazenamento. Os custos são agregados para todos os servidores do grupo avaliado.

    - As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, e nos seus discos e propriedades.
    - São apresentados custos mensais estimados para o cálculo e armazenamento.
    - A estimativa de custos é para executar os servidores no local como VMs infra-as-a-service (IaaS). A Avaliação do Servidor não considera os custos da plataforma como um serviço (PaaS) ou software-as-a-service (SaaS).

- Reveja as estimativas mensais de custos de armazenamento. Esta visão mostra os custos de armazenamento agregados para o grupo avaliado, divididos entre diferentes tipos de discos de armazenamento.
- Faça um exercício para ver detalhes para VMs específicos.

> [!NOTE]
> As classificações de confiança não são atribuídas a avaliações de servidores importados para a Avaliação do Servidor utilizando o CSV.

## <a name="supported-operating-system-names"></a>Nomes do sistema operativo suportados

Os nomes do sistema operativo fornecidos no CSV devem coincidir ou conter os nomes desta lista. Isto é necessário para que os nomes especificados sejam reconhecidos como válidos pela avaliação.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oráculo Linux 4/5<br/>
      Oráculo Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Chapéu Vermelho Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      Empresa SUSE Linux 11<br/>
      Empresa SUSE Linux 12<br/>
      Empresa SUSE Linux 8/9<br/>
      Empresa SUSE Linux 11<br/>
      SUSE abre SUSESUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Limiar do servidor do Windows<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows 10 Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Próximos passos

Neste tutorial:

> [!div class="checklist"]
> * Servidores importados para Azure Migrate: Avaliação do servidor utilizando CSV.
> * Criei e revendo uma avaliação.

Agora, [implemente um aparelho](./migrate-appliance.md) para avaliações mais precisas e reúna os servidores em grupos para uma avaliação mais profunda utilizando a análise da [dependência](./concepts-dependency-visualization.md).
