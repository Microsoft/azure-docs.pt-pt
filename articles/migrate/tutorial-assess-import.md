---
title: Avaliar servidores através de dados de servidor importados com a Avaliação do Servidor do Azure Migrate
description: Descreve como avaliar servidores no local para migração para o Azure com a Avaliação do Servidor do Azure Migrate através de dados importados.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: ff7e423063859a6cdc1a4362fb030c0deb75eb32
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658690"
---
# <a name="assess-servers-by-using-imported-data"></a>Avaliar servidores através de dados importados

Este artigo explica como avaliar servidores no local com a ferramenta [Azure Migrate: Avaliação do Servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool), ao importar metadados do servidor no formato de valores separados por vírgula (CSV). Este método de avaliação não exige que configure a aplicação do Azure Migrate para criar uma avaliação. É útil se:

- Pretende criar uma avaliação rápida e inicial antes de implementar a aplicação.
- Não pode implementar a aplicação do Azure Migrate na sua organização.
- Não pode partilhar credenciais que permitam o acesso a servidores no local.
- As restrições de segurança impedem que obtenha e envie dados recolhidos pela aplicação para o Azure. Pode controlar os dados que partilha num ficheiro importado. Além disso, grande parte dos dados (por exemplo,o fornecimento de endereços IP) é opcional.

## <a name="before-you-start"></a>Antes de começar

Lembre-se destes pontos:

- Pode adicionar até um máximo de 20 000 servidores num único ficheiro CSV.
- Pode adicionar até 20 000 servidores num projeto do Azure Migrate com CSV.
- Pode carregar informações do servidor para a Avaliação do Servidor várias vezes com CSV.
- A obtenção de informações da aplicação é útil para avaliar o seu ambiente no local para migração. No entanto, a Avaliação do Servidor não realiza atualmente a avaliação ao nível da aplicação nem tem em conta as aplicações ao criar uma avaliação.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configurar um projeto do Azure Migrate.
> * Preencher um ficheiro CSV com informações do servidor.
> * Importar o ficheiro para adicionar informações do servidor à Avaliação do Servidor.
> * Criar e rever uma avaliação.

> [!NOTE]
> Os tutoriais mostram o caminho de implementação mais simples para um cenário, para que possa configurar rapidamente uma prova de conceito. Os tutoriais utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os Manuais de instruções.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="set-azure-permissions-for-azure-migrate"></a>Definir permissões do Azure para o Azure Migrate

A sua conta do Azure precisa de permissões para criar um projeto do Azure Migrate.

1. No portal do Azure, abra a subscrição e selecione **Controlo de acesso (IAM)** .
2. Em **Verificar acesso**, localize a conta relevante e, em seguida, selecione-a para ver as permissões.
3. Confirme que tem as permissões de **Contribuidor** ou **Proprietário**.
    - Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir a função.

## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto do Azure Migrate

Para configurar um novo projeto do Azure Migrate:

1. No portal do Azure, em **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Na **Descrição geral**, em **Detetar, avaliar e migrar servidores**, selecione **Avaliar e migrar servidores**.

    ![Detetar e avaliar servidores](./media/tutorial-assess-import/assess-migrate.png)

4. Em **Introdução**, selecione **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
6. Em **Detalhes do projeto**, especifique o nome do projeto e a geografia na qual quer criar o projeto. Para obter mais informações:

    - Reveja as regiões suportadas em [clouds públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e do [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto do Azure Migrate](./media/tutorial-assess-import/migrate-project.png)

7. Selecione **Seguinte**.
8. Em **Selecionar ferramenta de avaliação**, **selecione Azure Migrate: Avaliação de Servidor** > **Seguinte**.

    ![Criar uma avaliação do Azure Migrate](./media/tutorial-assess-import/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Rever + adicionar ferramentas**, reveja as definições e, em seguida, selecione **Adicionar ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Vai então ser encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="prepare-the-csv"></a>Preparar o CSV

Transfira o modelo CSV e adicione informações do servidor ao mesmo.

### <a name="download-the-template"></a>Transferir o modelo

1. Em **Objetivos de Migração** > **Servidores** > **Azure Migrate: Avaliação do Servidor**, selecione **Detetar**.
2. Em **Detetar máquinas**, selecione **Importar com CSV**.
3. Selecione **Transferir** para transferir o modelo CSV. Como alternativa, pode [transferi-lo diretamente](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Transferir o modelo CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Adicionar informações do servidor

Obtenha dados do servidor e adicione-os ao ficheiro CSV.

- Para obter dados, pode exportá-los a partir de ferramentas que utiliza na gestão dos servidores no local, como o VMware vSphere ou a base de dados de gestão de configuração (CMDB).
- Para analisar os dados de exemplo, transfira o nosso [ficheiro de exemplo](https://go.microsoft.com/fwlink/?linkid=2108405).

A tabela seguinte resume os campos do ficheiro a preencher:

**Nome do campo** | **Obrigatório** | **Detalhes**
--- | --- | ---
**Nome do servidor** | Yes | Recomendamos que especifique o nome de domínio completamente qualificado (FQDN).
**Endereço IP** | No | Endereço do servidor.
**Núcleos** | Yes | Número de núcleos do processador alocados ao servidor.
**Memória** | Yes | Total de RAM, em MB, alocada ao servidor.
**Nome do SO** | Yes | Sistema operativo do servidor. <br/> Os nomes dos sistemas operativos que correspondem ou contêm os nomes [nesta](#supported-operating-system-names) lista são reconhecidos pela avaliação.
**Versão do SO** | No | Versão do sistema operativo do servidor.
**Arquitetura de SO** | No | Arquitetura de SO do servidor <br/> Os valores válidos são: x64, x86, amd64, 32 bits ou 64 bits
**Número de discos** | No | Não é necessário se forem indicados detalhes de discos individuais.
**Tamanho do disco 1**  | No | Tamanho máximo do disco, em GB.<br/>Pode adicionar detalhes de mais discos ao [adicionar colunas](#add-multiple-disks) ao modelo. Pode adicionar até oito discos.
**Operações de leitura do disco 1** | No | Operações de leitura do disco por segundo.
**Operações de escrita do disco 1** | No | Operações de escrita do disco por segundo.
**Débito de leitura do disco 1** | No | Dados lidos do disco por segundo, em MB por segundo.
**Débito de escrita do disco 1** | No | Dados escritos no disco por segundo, em MB por segundo.
**Percentagem de utilização da CPU** | No | Percentagem da CPU utilizada.
**Percentagem de utilização da memória** | No | Percentagem da RAM utilizada.
**Total de operações de leitura dos discos** | No | Operações de leitura por segundo de todos os discos combinados. <br/> Utilize este campo se não conseguir disponibilizar dados ao nível do disco. 
**Total de operações de escrita dos discos** | No | Operações de escrita por segundo de todos os discos combinados. <br/> Utilize este campo se não conseguir disponibilizar dados ao nível do disco.
**Total de débito de leitura dos discos** | No | Dados lidos de todos os discos, em MB por segundo. <br/> Utilize este campo se não conseguir disponibilizar dados ao nível do disco. 
**Total de débito de escrita dos discos** | No | Dados escritos em todos os discos, em MB por segundo. <br/> Utilize este campo se não conseguir disponibilizar dados ao nível do disco.
**Débito de Entrada na Rede** | No | Dados recebidos pelo servidor, em MB por segundo.
**Débito de Saída da Rede** | No | Dados transmitidos pelo servidor, em MB por segundo.
**Tipo de firmware** | No | Firmware do servidor. Os valores podem ser "BIOS" ou "UEFI".
**Endereço MAC**| No | Endereço MAC do servidor.


### <a name="add-operating-systems"></a>Adicionar sistemas operativos

A avaliação reconhece os nomes de sistemas operativos específicos. Qualquer nome especificado deve corresponder exatamente a uma das cadeias na [lista de nomes suportados](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Adicionar vários discos

O modelo disponibiliza campos predefinidos para o primeiro disco. Pode adicionar colunas semelhantes para até oito discos.

Por exemplo, para especificar todos os campos para um segundo disco, adicione estas colunas:

- Tamanho do disco 2
- Operações de leitura do disco 2
- Operações de escrita do disco 2
- Débito de leitura do disco 2
- Débito de escrita do disco 2

Se não conseguir indicar dados ao nível do disco, pode disponibilizar dados de desempenho do disco por servidor com os campos seguintes. Veja [esta](#add-server-information) secção para obter detalhes sobre cada campo.
- Total de operações de leitura dos discos
- Total de operações de escrita dos discos
- Total de débito de leitura dos discos
- Total de débito de escrita dos discos

## <a name="import-the-server-information"></a>Importar informações do servidor

Depois de adicionar informações ao modelo CSV, importe os servidores para a Avaliação do Servidor.

1. No Azure Migrate, em **Detetar máquinas**, aceda ao modelo concluído.
2. Selecione **Import** (Importar).
3. O estado da importação é mostrado.
    - Se forem apresentados avisos no estado, pode corrigi-los ou continuar sem os resolver.
    - Para melhorar a precisão da avaliação, melhore as informações do servidor conforme sugerido nos avisos.
    - Para ver e corrigir os avisos, selecione **Transferir .CSV dos detalhes dos avisos**. Esta operação transfere o CSV com os avisos incluídos. Analise os avisos e corrija os problemas conforme necessário.
    - Se forem apresentados erros no estado que fazem com que o estado da importação seja **Com falha**, tem de os corrigir para poder continuar com a importação:
        1. Transfira o CSV, que agora inclui os detalhes dos erros.
        1. Analise e resolva os erros conforme necessário. 
        1. Carregue o ficheiro modificado novamente.
4. Quando o estado da importação for **Concluído**, as informações do servidor foram importadas.

## <a name="update-server-information"></a>Atualizar informações do servidor

Pode atualizar as informações de um servidor ao importar os respetivos dados novamente com o mesmo **Nome do servidor**. Não pode modificar o campo **Nome do servidor**. A eliminação de servidores não é atualmente suportada.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Para verificar se os servidores aparecem no portal do Azure após a deteção:

1. Abra o dashboard do Azure Migrate.
2. Na página **Azure Migrate – Servidores** > **Azure Migrate: Avaliação do Servidor**, selecione o ícone que mostra a contagem de **Servidores detetados**.
3. Selecione o separador **Com base na importação**.

## <a name="set-up-and-run-an-assessment"></a>Configurar e executar uma avaliação

Pode criar dois tipos de avaliações com a Avaliação do Servidor.


**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais do Azure. <br/><br/> Pode avaliar as [VMs VMware](how-to-set-up-appliance-vmware.md) no local, as [VMs Hyper-V](how-to-set-up-appliance-hyper-v.md) e os [servidores físicos](how-to-set-up-appliance-physical.md) para a migração para o Azure com este tipo de avaliação.
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para o [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pode avaliar as [VMs VMware](how-to-set-up-appliance-vmware.md) no local para a migração para o Azure VMware Solution (AVS) com este tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

### <a name="sizing-criteria"></a>Critérios de dimensionamento

A Avaliação do Servidor disponibiliza duas opções de critérios de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho recolhidos | **Avaliação da VM do Azure**: A recomendação de tamanho da VM baseia-se nos dados de utilização da CPU e da memória.<br/><br/> A recomendação do tipo de disco (HDD/SSD padrão ou discos geridos premium) baseia-se no IOPS e no débito dos discos no local.<br/><br/> **Avaliação do Azure VMware Solution (AVS)** : A recomendação de nós AVS baseia-se nos dados de utilização da CPU e da memória.
**Como está no local** | Avaliações que não utilizam dados de desempenho para fazer recomendações. | **Avaliação da VM do Azure**: A recomendação de tamanho da VM baseia-se no tamanho da VM no local<br/><br> O tipo de disco recomendado baseia-se naquilo que seleciona na definição do tipo de armazenamento para a avaliação.<br/><br/> **Avaliação do Azure VMware Solution (AVS)** : A recomendação de nós AVS baseia-se no tamanho da VM no local.


Para executar uma avaliação:

1. Reveja as [melhores práticas](best-practices-assessment.md) para criar avaliações.
2. No separador **Servidores**, no mosaico **Azure Migrate: Avaliação do Servidor**, selecione **Avaliar**.

    ![Avaliação](./media/tutorial-assess-physical/assess.png)

3. Em **Avaliar servidores**, especifique o nome da avaliação e selecione o tipo de **avaliação** como *VM do Azure*, caso pretenda executar avaliações da VM do Azure, ou *Azure VMware Solution (AVS)* , caso pretenda realizar avaliações do AVS.

    ![Noções Básicas de Avaliação](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Em **Origem da deteção**, selecione **Máquinas adicionadas através da importação para o Azure Migrate**.

5. Selecione **Ver tudo** para rever as propriedades da avaliação.

    ![Propriedades da avaliação](./media/tutorial-assess-physical/view-all.png)

6. Clique em **seguinte** para **Selecionar as máquinas a avaliar**. Em **Selecionar ou criar um grupo**, selecione **Criar Novo** e especifique um nome do grupo. Um grupo reúne uma ou mais VMs para avaliação.
7. Em **Adicionar máquinas ao grupo**, selecione os servidores a serem adicionados ao grupo.
8. Clique em **seguinte** para **Rever + criar avaliação** e reveja os detalhes da avaliação.
9. Clique em **Criar avaliação**, para criar o grupo e, em seguida, executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-physical/assessment-create.png)

9. Após a criação da avaliação, veja-a em **Servidor** > **Azure Migrate: Avaliação do Servidor** > **Avaliações**.
10. Selecione **Exportar avaliação** para a transferir como um ficheiro do Microsoft Excel.

Para saber mais detalhes sobre a avaliação do **Azure VMware Solution (AVS)** , veja [aqui](how-to-create-azure-vmware-solution-assessment.md). 

## <a name="review-an-azure-vm-assessment"></a>Rever uma avaliação de VM do Azure

Uma avaliação de VM do Azure descreve:

- **Preparação para o Azure**: Se os servidores são ou não adequados para a migração para o Azure.
- **Estimativa de custo mensal**: Custos de computação e armazenamento mensais estimados para executar os servidores no Azure.
- **Estimativa do custo de armazenamento mensal**: Custos estimados do armazenamento em disco após a migração.

### <a name="view-an-assessment"></a>Ver uma avaliação

1. Em **Objetivos da migração** > **Servidores**, selecione **Avaliações** em **Azure Migrate: Avaliação do Servidor**.
2. Em **Avaliações**, selecione uma avaliação para a abrir.

    ![Resumo da avaliação](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Rever a preparação para o Azure

1. Na **Preparação para o Azure**, determine se os servidores estão prontos para a migração para o Azure.
2. Reveja o estado:
    - **Preparada para o Azure**: O Azure Migrate recomenda um tamanho de VM e estimativas de custo para VMs na avaliação.
    - **Preparada com condições**: Mostra problemas e a remediação sugerida.
    - **Não está preparada para o Azure**: Mostra problemas e a remediação sugerida.
    - **Preparação desconhecida**: O Azure Migrate não pode avaliar a preparação devido a problemas de disponibilidade de dados.

3. Selecione um estado de **Preparação para o Azure**. Pode ver os detalhes de preparação do servidor e desagregar para ver os detalhes do servidor, incluindo as definições de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Rever os detalhes dos custos

Esta vista mostra o custo estimado de computação e armazenamento da execução das VMs no Azure. Pode:

- Rever os custos mensais de computação e armazenamento. Os custos são agregados para todos os servidores no grupo avaliado.

    - As estimativas de custo baseiam-se nas recomendações de tamanho para uma máquina e os respetivos discos e propriedades.
    - Os custos mensais estimados para computação e armazenamento são mostrados.
    - A estimativa de custo refere-se à execução de servidores no local como VMs de infraestrutura como serviço (IaaS). A Avaliação do Servidor não considera os custos de plataforma como serviço (PaaS) ou software como serviço (SaaS).

- Rever as estimativas de custo de armazenamento mensal. Esta vista mostra os custos de armazenamento agregados do grupo avaliado, divididos entre diferentes tipos de discos de armazenamento.
- Desagregue para ver detalhes de VMs específicas.

> [!NOTE]
> Não são atribuídas classificações de confiança a avaliações de servidores importadas para a Avaliação do Servidor com CSV.

## <a name="supported-operating-system-names"></a>Nomes de sistemas operativos suportados

Os nomes de sistemas operativos indicados no CSV devem corresponder ou conter os nomes nesta lista. Tal é necessário para que os nomes especificados sejam reconhecidos como válidos pela avaliação.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A–H**
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
      **I–R**
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
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
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
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S–T**
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
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U–Z**
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
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows 10 Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Importou servidores para o Azure Migrate: Avaliação do Servidor com CSV.
> * Criou e reviu uma avaliação.

Agora, [implemente uma aplicação](./migrate-appliance.md) para avaliações mais precisas e agrupe servidores para uma avaliação mais profunda com a [análise de dependência](./concepts-dependency-visualization.md).
