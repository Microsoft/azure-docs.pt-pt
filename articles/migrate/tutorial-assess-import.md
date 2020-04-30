---
title: Avaliar servidores utilizando dados do servidor importados com avaliação do servidor migratório Azure
description: Descreve como avaliar os servidores no local para migração para Azure com a Avaliação do Servidor Migratório Azure utilizando dados importados.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 484dfd7834a206dce6805dc38b0eabeae2ee352a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82114569"
---
# <a name="assess-servers-by-using-imported-data"></a>Avaliar servidores utilizando dados importados

Este artigo explica como avaliar os servidores no local com a ferramenta De avaliação do [servidor Azure Migração,](migrate-services-overview.md#azure-migrate-server-assessment-tool) importando metadados do servidor em formato de valores separados em vírem (CSV). Este método de avaliação não requer que você crie o aparelho Migratório Azure para criar uma avaliação. É útil se:

- Pretende criar uma avaliação rápida e inicial antes de utilizar o aparelho.
- Não pode implantar o aparelho Azure Migrate na sua organização.
- Não pode partilhar credenciais que permitem o acesso aos servidores no local.
- As restrições de segurança impedem-no de recolher e enviar dados recolhidos pelo aparelho para o Azure. Pode controlar os dados que partilha num ficheiro importado. Além disso, grande parte dos dados (por exemplo, fornecer endereços IP) é opcional.

## <a name="before-you-start"></a>Antes de começar

Esteja atento a estes pontos:

- Pode adicionar um máximo de 20.000 servidores num único ficheiro CSV.
- Pode adicionar até 20.000 servidores num projeto Azure Migrate utilizando cSV.
- Pode enviar várias vezes as informações do servidor para a Avaliação do Servidor utilizando o CSV.
- Recolher informações sobre aplicações é útil na avaliação do seu ambiente no local para migração. No entanto, a Avaliação do Servidor não realiza atualmente a avaliação do nível de aplicação ou tem em conta as aplicações ao criar uma avaliação.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um projeto Azure Migrate.
> * Preencha um ficheiro CSV com informações do servidor.
> * Importe o ficheiro para adicionar informações do servidor à Avaliação do Servidor.
> * Criar e rever uma avaliação.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário, para que possa rapidamente configurar uma prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, reveja os guias como fazer.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="set-azure-permissions-for-azure-migrate"></a>Definir permissões azure para Azure Migrate

A sua conta Azure precisa de permissões para criar um projeto Azure Migrate.

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)**.
2. No **Check access,** encontre a conta relevante e, em seguida, selecione-a para visualizar permissões.
3. Certifique-se de que tem permissões **de Contribuinte** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.

## <a name="set-up-an-azure-migrate-project"></a>Criar um projeto Azure Migrate

Para criar um novo projeto Azure Migrate:

1. No portal Azure, em **Todos os serviços,** procure **o Azure Migrate.**
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **visão geral**, em **Discover, avalie e emigra os servidores,** selecione **avaliar e migrar servidores**.

    ![Descubra e avalie servidores](./media/tutorial-assess-import/assess-migrate.png)

4. Em **Início,** selecione **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
6. Em DETALHES DO **PROJETO,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Para obter mais informações:

    - Reveja as geografias apoiadas para as nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-import/migrate-project.png)

7. Selecione **Seguinte**.
8. Na **ferramenta de avaliação Select,** selecione **Azure Migrate: Server Assessment** > **Next**.

    ![Criar uma avaliação de Migração Azure](./media/tutorial-assess-import/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Rever + adicionar ferramentas,** reveja as definições e, em seguida, selecione Adicionar **ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Em seguida, será levado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="prepare-the-csv"></a>Preparar o CSV

Descarregue o modelo CSV e adicione informações do servidor.

### <a name="download-the-template"></a>Transferir o modelo

1. Em **Objetivos** > de Migração**Servidores** > **Azure Migrar: Avaliação do servidor**, selecione **Discover**.
2. Nas **máquinas Discover,** selecione **Importar utilizando CSV**.
3. Selecione **Baixar** para descarregar o modelo CSV. Em alternativa, pode [descarregá-lo diretamente](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Descarregue o modelo CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Adicionar informações do servidor

Recolha os dados do servidor e adicione-os ao ficheiro CSV.

- Para recolher dados, pode exportá-lo a partir de ferramentas que utiliza para a gestão de servidores no local, como vSphere VMware ou a sua base de dados de gestão de configuração (CMDB).
- Para rever os dados da amostra, descarregue o nosso [ficheiro de exemplo.](https://go.microsoft.com/fwlink/?linkid=2108405)

A tabela seguinte resume os campos de arquivo para preencher:

**Nome do campo** | **Obrigatório** | **Detalhes**
--- | --- | ---
**Nome do servidor** | Sim | Recomendamos especificar o nome de domínio totalmente qualificado (FQDN).
**Endereço IP** | Não | Endereço do servidor.
**Núcleos** | Sim | Número de núcleos de processador atribuídos ao servidor.
**Memória** | Sim | RAM total, em MB, atribuído ao servidor.
**Nome os** | Sim | Sistema operativo do servidor. <br/> Os nomes do sistema operativo que correspondam ou contêm os nomes [desta](#supported-operating-system-names) lista são reconhecidos pela avaliação.
**Versão do SO** | Não | Versão do sistema operativo do servidor.
**Número de discos** | Não | Não é necessário se forem fornecidos detalhes individuais do disco.
**Tamanho do disco 1**  | Não | Tamanho máximo do disco, em GB.<br/>Pode adicionar detalhes para mais discos [adicionando colunas](#add-multiple-disks) no modelo. Pode adicionar até oito discos.
**Disque1 leitura de operações** | Não | O disco lê operações por segundo.
**Disque 1 write ops** | Não | Operações de escrita de disco por segundo.
**Entrada de leitura do disco 1** | Não | Os dados são lidos a partir do disco por segundo, em MB por segundo.
**Entrada de escrita de disco 1** | Não | Dados escritos ao disco por segundo, em MB por segundo.
**Percentagem de utilização do CPU** | Não | Percentagem de CPU utilizada.
**Percentagem de utilização da memória** | Não | Percentagem de RAM usada.
**Total de discos de leitura ops** | Não | Operações de leitura de disco por segundo.
**Total de discos escrevem operações** | Não | Operações de escrita de disco por segundo.
**Total de discos lidos através de produção** | Não | Os dados são lidos a partir do disco, em MB por segundo.
**Total de discos escrevem a produção** | Não | Dados escritos em disco, em MB por segundo.
**Entrada em rede** | Não | Dados recebidos pelo servidor, em MB por segundo.
**Entrada de rede out** | Não | Dados transmitidos pelo servidor, em MB por segundo.
**Tipo firmware** | Não | Firmware de servidor. Os valores podem ser "BIOS" ou "UEFI".
**Endereço MAC**| Não | Endereço MAC do servidor.


### <a name="add-operating-systems"></a>Adicionar sistemas operativos

A avaliação reconhece nomes específicos do sistema operativo. Qualquer nome que especifique deve coincidir exatamente com uma das cordas da lista de [nomes suportados](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Adicione vários discos

O modelo fornece campos padrão para o primeiro disco. Pode adicionar colunas semelhantes para até oito discos.

Por exemplo, para especificar todos os campos para um segundo disco, adicione estas colunas:

- Disco 2 tamanho
- Disquei2 leitura de operações
- Disque 2 write ops
- Entrada de leitura do disco 2
- Disque 2 escrever a entrada


## <a name="import-the-server-information"></a>Importar as informações do servidor

Depois de adicionar informações ao modelo CSV, importe os servidores para a Avaliação do Servidor.

1. Em Azure Migrate, em **Máquinas Discover,** vá ao modelo completo.
2. Selecione **Importar**.
3. O estado de importação é demonstrado.
    - Se os avisos aparecerem no estado, pode corrigi-los ou continuar sem os abordar.
    - Para melhorar a precisão da avaliação, melhore a informação do servidor conforme sugerido nos avisos.
    - Para visualizar e corrigir avisos, selecione detalhes de **aviso de descarregamento . CSV.** Esta operação descarrega o CSV com avisos incluídos. Reveja os avisos e corrija as questões conforme necessário.
    - Se surgirem erros no estado de modo a que o estado de importação seja **falhado,** deve corrigir esses erros antes de poder continuar com a importação:
        1. Descarregue o CSV, que agora inclui detalhes de erro.
        1. Reveja e aborde os erros conforme necessário. 
        1. Faça o upload do ficheiro modificado novamente.
4. Quando o estado de importação estiver **concluído,** as informações do servidor foram importadas.

## <a name="update-server-information"></a>Atualizar informações do servidor

Pode atualizar as informações para um servidor importando novamente os dados para o servidor com o mesmo **nome de Servidor**. Não pode modificar o campo de nome do **Servidor.** Atualmente, os servidores de desminagem não são suportados.

## <a name="verify-servers-in-the-portal"></a>Verifique os servidores no portal

Para verificar se os servidores aparecem no portal Azure após a descoberta:

1. Abra o painel de migração Azure.
2. Na página **Azure Migrate - Servers** > **Azure Migrate: Servers Emigrar: Página** de Avaliação do Servidor, selecione o ícone que apresenta a contagem para **servidores descobertos**.
3. Selecione o separador **baseado em Importação.**

## <a name="set-up-and-run-an-assessment"></a>Configurar e fazer uma avaliação

Pode criar dois tipos de avaliações utilizando a Avaliação do Servidor.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações baseadas em valores de dados de desempenho especificados. | **Tamanho VM recomendado**: Baseado em CPU e dados de utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido por série ou premium)**: Baseado na entrada/saída por segundo (IOPS) e na entrada dos discos no local.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho VM recomendado**: Com base no tamanho do servidor especificado.<br/><br> **Tipo**de disco recomendado : Com base na definição do tipo de armazenamento selecionada para a avaliação.

Para efazer uma avaliação:

1. Reveja as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. No separador **Servidores,** no **azulejo Azure Migrate: Server Assessment,** selecione **Avaliar**.

    ![Avaliar](./media/tutorial-assess-physical/assess.png)

3. Em **avaliar os servidores,** especifique um nome para a avaliação.
4. Na **fonte Discovery,** selecione **Máquinas adicionadas através da importação para o Azure Migrate**.
5. Selecione **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-physical/view-all.png)

6. Em **Selecionar ou criar um grupo,** selecione Criar **Novo**e especifique um nome de grupo. Um grupo reúne um ou mais VMs para avaliação.
7. Em **Adicionar máquinas ao grupo,** selecione servidores para adicionar ao grupo.
8. Selecione **Criar avaliação** para criar o grupo e, em seguida, executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-physical/assessment-create.png)

9. Após a criação da avaliação, veja-a em **Servers** > **Azure Migrate:** > **Avaliações**de Avaliação do Servidor .
10. Selecione **avaliação de Exportação** para descarregá-lo como um ficheiro Microsoft Excel.

## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azure**: Se os servidores são adequados para migração para Azure.
- **Estimativa mensal de custos**: Estimativa mensal de cálculo e armazenamento para executar os servidores em Azure.
- **Estimativa mensal**do custo do armazenamento : Custos estimados para armazenamento de disco após migração.

### <a name="view-an-assessment"></a>Ver uma avaliação

1. Em **objetivos** > de migração**Servidores**, selecione **Avaliações** em **Azure Migrate: Server Assessment**.
2. Em **Avaliações,** selecione uma avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Rever a prontidão do Azure

1. Na **prontidão do Azure,** determine se os servidores estão prontos para a migração para O Azure.
2. Reveja o estado:
    - **Pronto para Azure**: A Azure Migrate recomenda uma estimativa de tamanho VM e custos para os VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e sugeriu reparação.
    - **Não está pronto para O Azure:** Mostra problemas e sugeriu reparação.
    - **Prontidão desconhecida**: O Azure Migrate não pode avaliar a prontidão, devido a problemas de disponibilidade de dados.

3. Selecione um estado de **prontidão Azure.** Pode visualizar detalhes de prontidão do servidor e perfurar para ver detalhes do servidor, incluindo definições de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Analisar detalhes de custos

Esta visão mostra o cálculo estimado e o custo de armazenamento de vMs de funcionamento em Azure. Pode:

- Reveja os custos mensais de cálculo e armazenamento. Os custos são agregados para todos os servidores do grupo avaliado.

    - As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, e nos seus discos e propriedades.
    - Os custos mensais estimados para a computação e armazenamento são apresentados.
    - A estimativa de custos é para executar os servidores no local como VMs de infra-estrutura-como-um-serviço (IaaS). A Avaliação do Servidor não considera os custos da plataforma como um serviço (PaaS) ou do software-as-a-service (SaaS).

- Reveja as estimativas mensais de custos de armazenamento. Esta vista mostra os custos de armazenamento agregados para o grupo avaliado, divididos entre diferentes tipos de discos de armazenamento.
- Faça um furo para ver detalhes para VMs específicos.

> [!NOTE]
> As classificações de confiança não são atribuídas a avaliações de servidores importados para a Avaliação do Servidor utilizando o CSV.

## <a name="supported-operating-system-names"></a>Nomes de sistema operativo suportado

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
      Centos 4/5
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
       Oracle Linux 4/5<br/>
      Oráculo Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Empresa de Chapéu Vermelho Linux 5<br/>
      Empresa de Chapéu Vermelho Linux 6<br/>
      Empresa de Chapéu Vermelho Linux 7<br/>
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
      Solaris 8<br/>
      Solarsolar solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE abre SUSE
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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Servidores importados para Azure Migrate: Avaliação do servidor utilizando CSV.
> * Criou e reviu uma avaliação.

Agora, [implante um aparelho](./migrate-appliance.md) para avaliações mais precisas e junte os servidores em grupos para uma avaliação mais profunda utilizando a análise da [dependência](./concepts-dependency-visualization.md).
