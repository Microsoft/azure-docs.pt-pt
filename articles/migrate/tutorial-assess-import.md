---
title: Avaliar servidores usando dados de servidor importados com a avaliação de servidor de migrações para Azure
description: Descreve como avaliar servidores locais para migração para o Azure com a avaliação de servidor de migrações para Azure usando dados importados.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 060399952545c903fec8ecf08d99e438883c9fd1
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902544"
---
# <a name="assess-servers-by-using-imported-data"></a>Avaliar servidores usando dados importados

Este artigo explica como avaliar servidores locais com a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) , importando os metadados do servidor em formato CSV (valores separados por vírgula). Esse método de avaliação não exige que você configure o dispositivo de migrações para Azure para criar uma avaliação. É útil se:

- Você deseja criar uma avaliação rápida e inicial antes de implantar o dispositivo.
- Você não pode implantar o dispositivo de migrações para Azure em sua organização.
- Você não pode compartilhar credenciais que permitam o acesso a servidores locais.
- As restrições de segurança impedem que você colete e envie dados coletados pelo dispositivo para o Azure. Você pode controlar os dados que compartilha em um arquivo importado. Além disso, grande parte dos dados (por exemplo, fornecimento de endereços IP) é opcional.

## <a name="before-you-start"></a>Antes de começar

Lembre-se destes pontos:

- Você pode adicionar até um máximo de 20.000 servidores em um único arquivo CSV.
- Você pode adicionar até 20.000 servidores em um projeto de migrações para Azure usando CSV.
- Você pode carregar informações do servidor para a avaliação do servidor várias vezes usando CSV.
- A coleta de informações do aplicativo é útil para avaliar seu ambiente local para migração. No entanto, a avaliação do servidor não executa atualmente a avaliação no nível do aplicativo ou leva os aplicativos em conta ao criar uma avaliação.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure um projeto de migrações para Azure.
> * Preencha um arquivo CSV com as informações do servidor.
> * Importe o arquivo para adicionar informações do servidor à avaliação do servidor.
> * Crie e revise uma avaliação.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, examine os guias de instruções.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="set-azure-permissions-for-azure-migrate"></a>Definir permissões do Azure para migrações para Azure

Sua conta do Azure precisa de permissões para criar um projeto de migrações para Azure.

1. No portal do Azure, abra a assinatura e selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e, em seguida, selecione-a para exibir permissões.
3. Verifique se você tem permissões de **colaborador** ou **proprietário** .
    - Se você acabou de criar uma conta gratuita do Azure, você é o proprietário da sua assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto de migrações para Azure

Para configurar um novo projeto de migrações para Azure:

1. No portal do Azure, em **todos os serviços**, pesquise **migrações para Azure**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **visão geral**, em **descobrir, avaliar e migrar servidores**, selecione **avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-assess-import/assess-migrate.png)

4. Em **introdução**, selecione **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
6. Em **detalhes do projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto. Para mais informações:

    - Examine as [geografias com suporte](migrate-support-matrix.md#supported-geographies). A geografia do projeto só é utilizada para armazenar os metadados recolhidos das VMs no local.
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-import/migrate-project.png)

7. Selecione **Seguinte**.
8. Em **selecionar ferramenta de avaliação**, selecione **migrações para Azure: avaliação do servidor** > **Avançar**.

    ![Criar uma avaliação de migrações para Azure](./media/tutorial-assess-import/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **examinar + adicionar ferramentas**, examine as configurações e, em seguida, selecione **Adicionar ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Em seguida, você será levado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="prepare-the-csv"></a>Preparar o CSV

Baixe o modelo CSV e adicione informações do servidor a ele.

### <a name="download-the-template"></a>Transferir o modelo

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, selecione **descobrir**.
2. Em **descobrir máquinas**, selecione **importar usando CSV**.
3. Selecione **baixar** para baixar o modelo CSV. Como alternativa, você pode [baixá-lo diretamente](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Baixar modelo CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Adicionar informações do servidor

Coletar dados do servidor e adicioná-los ao arquivo CSV.

- Para coletar dados, você pode exportá-los de ferramentas usadas para o gerenciamento de servidor local, como VMware vSphere ou o banco de dados de gerenciamento de configuração (CMDB).
- Para examinar os dados de exemplo, baixe nosso [arquivo de exemplo](https://go.microsoft.com/fwlink/?linkid=2108405).

A tabela a seguir resume os campos de arquivo para preencher:

**Nome do campo** | **Obrigatório** | **Detalhes**
--- | --- | ---
**Nome do servidor** | Sim | É recomendável especificar o FQDN (nome de domínio totalmente qualificado).
**Endereço IP** | Não | Endereço do servidor.
**Núcleos** | Sim | Número de núcleos de processador alocados para o servidor.
**Memória** | Sim | Total de RAM, em MB, alocada para o servidor.
**Nome do so** | Sim | Sistema operacional do servidor.
**Versão do SO** | Não | Versão do sistema operacional do servidor.
**Número de discos** | Não | Não será necessário se detalhes de disco individuais forem fornecidos.
**Tamanho do disco 1**  | Não | Tamanho máximo do disco, em GB.<br/>Você pode adicionar detalhes para mais discos [adicionando colunas](#add-multiple-disks) no modelo. Você pode adicionar até oito discos.
**Operações de leitura de disco 1** | Não | Operações de leitura de disco por segundo.
**Operações de gravação do disco 1** | Não | Operações de gravação de disco por segundo.
**Taxa de transferência de leitura do disco 1** | Não | Dados lidos do disco por segundo, em MB por segundo.
**Taxa de transferência de gravação do disco 1** | Não | Dados gravados no disco por segundo, em MB por segundo.
**Porcentagem de utilização da CPU** | Não | Porcentagem da CPU usada.
**Porcentagem de utilização da memória** | Não | Porcentagem de RAM usada.
**Total de operações de leitura de discos** | Não | Operações de leitura de disco por segundo.
**Total de operações de gravação de discos** | Não | Operações de gravação de disco por segundo.
**Taxa de transferência de leitura do total de discos** | Não | Dados lidos do disco, em MB por segundo.
**Taxa de transferência de gravação total de discos** | Não | Dados gravados no disco, em MB por segundo.
**Rede na taxa de transferência** | Não | Dados recebidos pelo servidor, em MB por segundo.
**Taxa de transferência de saída da rede** | Não | Dados transmitidos pelo servidor, em MB por segundo.
**Tipo de firmware** | Não | Firmware do servidor. Os valores podem ser "BIOS" ou "UEFI".
**Tipo de servidor** | Não | Os valores podem ser "físico" ou "virtual".
**Visor** | Não | Hipervisor no qual um computador está em execução. <br/> Os valores podem ser "VMware", "Hyper-V", "Xen", "AWS", "GCP" ou "other".
**Número de versão do hipervisor** | Não | Versão do hipervisor.
**ID da máquina virtual** | Não | Identificador de VM. Esse é o valor de **InstanceUUid** para uma VM VMware vCenter ou a **ID de VM do Hyper-v** para o Hyper-v.
**ID do Virtual Machine Manager** | Não | Esse é o valor de **InstanceUUid** para o VMware vCenter. Ele não é necessário para o Hyper-V.
**Endereço MAC**| Não | Endereço MAC do servidor.
**ID DO BIOS** | Não | ID do BIOS do servidor.
**ID do servidor personalizado** | Não | ID de servidor local e exclusiva localmente. <br/> Útil para acompanhar o servidor importado pela ID local.
**Nome do aplicativo 1** | Não | Nome da carga de trabalho em execução no servidor.<br/>Você pode adicionar detalhes para mais aplicativos [adicionando colunas](#add-multiple-applications) no modelo. Você pode adicionar até cinco aplicativos.
**Tipo de aplicativo 1** | Não | Tipo de carga de trabalho em execução no servidor
**Versão do aplicativo 1** | Não | Versão da carga de trabalho em execução no servidor.
**Expiração da licença do aplicativo 1** | Não | Expiração de licença da carga de trabalho (se aplicável).
**Unidade de negócios** | Não | Unidade de negócios à qual o servidor pertence.
**Proprietário da empresa** | Não | Proprietário da unidade de negócios.
**Nome do aplicativo de negócios** | Não | Nome do aplicativo ao qual o aplicativo pertence.
**Localização** | Não | Datacenter no qual o servidor está localizado.
**Data de descomissionamento do servidor** | Não | Data de descomissionamento do servidor físico ou do servidor físico subjacente do servidor virtual.

### <a name="add-operating-systems"></a>Adicionar os sistemas operacionais

A avaliação reconhece nomes específicos do sistema operacional. Qualquer nome especificado deve corresponder exatamente a uma das cadeias de caracteres na [lista de nomes com suporte](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Adicionar vários discos

O modelo fornece campos padrão para o primeiro disco. Você pode adicionar colunas semelhantes para até oito discos.

Por exemplo, para especificar todos os campos para um segundo disco, adicione estas colunas:

- Tamanho do disco 2
- Operações de leitura de disco 2
- Operações de gravação de disco 2
- Taxa de transferência de leitura do disco 2
- Taxa de transferência de gravação do disco 2

### <a name="add-multiple-applications"></a>Adicionar vários aplicativos

O modelo fornece campos para um único aplicativo. Você pode adicionar colunas semelhantes para até cinco aplicativos.  

Por exemplo, para especificar todos os campos para um segundo aplicativo, adicione estas colunas:

- Nome do aplicativo 2
- Tipo de aplicativo 2
- Versão do aplicativo 2
- Expiração da licença do aplicativo 2

> [!NOTE]
> As informações do aplicativo são úteis para avaliar seu ambiente local para migração. No entanto, a avaliação do servidor de migrações para Azure não executa atualmente a avaliação no nível do aplicativo ou leva os aplicativos em conta ao criar uma avaliação.

## <a name="import-the-server-information"></a>Importar as informações do servidor

Depois de adicionar informações ao modelo CSV, importe os servidores para a avaliação do servidor.

1. Em migrações para Azure, em **descobrir máquinas**, vá para o modelo concluído.
2. Selecione **Import** (Importar).
3. O status de importação é mostrado.
    - Se os avisos aparecerem no status, você poderá corrigi-los ou continuar sem solucioná-los.
    - Para melhorar a precisão da avaliação, melhore as informações do servidor como sugeridas em avisos.
    - Para exibir e corrigir avisos, selecione **baixar detalhes do aviso. CSV**. Esta operação baixa o CSV com avisos incluídos. Examine os avisos e corrija os problemas conforme necessário.
    - Se forem exibidos erros no status para que o status da importação seja **falhado**, você deverá corrigir esses erros para poder continuar com a importação:
        1. Baixe o CSV, que agora inclui detalhes do erro.
        1. Examine e resolva os erros conforme necessário. 
        1. Carregue o arquivo modificado novamente.
4. Quando o status da importação for **concluído**, as informações do servidor foram importadas.

## <a name="update-server-information"></a>Atualizar informações do servidor

Você pode atualizar as informações de um servidor importando os dados para o servidor novamente com o mesmo **nome de servidor**. Não é possível modificar o campo **nome do servidor** . Atualmente, não há suporte para a exclusão de servidores.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Para verificar se os servidores aparecem no portal do Azure após a descoberta:

1. Abra o painel migrações para Azure.
2. Na página **migrar do Azure-servidores** > **migrações para Azure: avaliação do servidor** , selecione o ícone que exibe a contagem de **servidores descobertos**.
3. Selecione a guia **com base na importação** .

## <a name="set-up-and-run-an-assessment"></a>Configurar e executar uma avaliação

Você pode criar dois tipos de avaliações usando a avaliação do servidor.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações baseadas em valores de dados de desempenho especificados. | **Tamanho de VM recomendado**: com base nos dados de uso de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base no IOPS (entrada/saída por segundo) e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: com base no tamanho do servidor especificado.<br/><br> **Tipo de disco recomendado**: com base na configuração de tipo de armazenamento que você selecionar para a avaliação.

Para executar uma avaliação:

1. Examine as [práticas recomendadas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **servidores** , no bloco **migrações para Azure: avaliação do servidor** , selecione **avaliar**.

    ![Avaliar](./media/tutorial-assess-physical/assess.png)

3. Em **avaliar servidores**, especifique um nome para a avaliação.
4. Em **origem da descoberta**, selecione **computadores adicionados por meio de importar para migrações para Azure**.
5. Selecione **Exibir tudo** para examinar as propriedades de avaliação.

    ![Propriedades da avaliação](./media/tutorial-assess-physical/view-all.png)

6. Em **selecionar ou criar um grupo**, selecione **criar novo**e especifique um nome de grupo. Um grupo reúne uma ou mais VMs juntas para avaliação.
7. Em **Adicionar computadores ao grupo**, selecione os servidores a serem adicionados ao grupo.
8. Selecione **criar avaliação** para criar o grupo e, em seguida, execute a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-physical/assessment-create.png)

9. Após a criação da avaliação, exiba-a em **servidores** > **migrações para Azure:** **avaliações de > de**avaliação do servidor.
10. Selecione **Exportar avaliação** para baixá-lo como um arquivo do Microsoft Excel.

## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação do Azure**: se os servidores são adequados para a migração para o Azure.
- **Estimativa de custo mensal**: custos estimados de computação mensal e armazenamento para executar os servidores no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para armazenamento em disco após a migração.

### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **metas de migração** > **servidores**, selecione **avaliações** em **migrações para Azure: avaliação do servidor**.
2. Em **avaliações**, selecione uma avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a preparação do Azure

1. Na **preparação do Azure**, determine se os servidores estão prontos para a migração para o Azure.
2. Examine o status:
    - **Pronto para o Azure**: as migrações para Azure recomendam um tamanho de VM e estimativas de custo para VMs na avaliação.
    - **Pronto com condições**: mostra problemas e correção sugerida.
    - **Não está pronto para o Azure**: mostra problemas e correção sugerida.
    - **Preparação desconhecida**: as migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

3. Selecione um status de **preparação do Azure** . Você pode exibir os detalhes de preparação do servidor e fazer uma busca detalhada para ver os detalhes do servidor, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Examinar detalhes de custo

Essa exibição mostra o custo estimado de computação e armazenamento de VMs em execução no Azure. Pode:

- Examine os custos mensais de computação e armazenamento. Os custos são agregados para todos os servidores no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um computador e seus discos e propriedades.
    - Os custos mensais estimados para computação e armazenamento são mostrados.
    - A estimativa de custo é para executar os servidores locais como VMs de IaaS (infraestrutura como serviço). A avaliação do servidor não considera os custos de plataforma como serviço (PaaS) ou software como serviço (SaaS).

- Revisar estimativas de custo de armazenamento mensal. Essa exibição mostra os custos de armazenamento agregados para o grupo avaliado, divididos entre diferentes tipos de discos de armazenamento.
- Faça uma busca detalhada para ver os detalhes de VMs específicas.

> [!NOTE]
> As classificações de confiança não são atribuídas a avaliações de servidores importados para a avaliação do servidor usando CSV.

## <a name="supported-operating-system-names"></a>Nomes de sistema operacional com suporte

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A-H**
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
      **I-R**
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
      **S-T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity sistemas eComStation 1<br/>
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
      **U-Z**
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
      Limite do Windows Server<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Servidores importados para migrações para Azure: avaliação de servidor usando CSV.
> * Criado e revisado uma avaliação.

Agora, [implante um dispositivo](./migrate-appliance.md) para obter avaliações mais precisas e reúna servidores em grupos para uma avaliação mais profunda usando a [análise de dependência](./concepts-dependency-visualization.md).
