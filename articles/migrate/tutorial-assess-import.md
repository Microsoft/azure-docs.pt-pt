---
title: Avaliar servidores usando dados de servidor importados com a avaliação de servidor de migrações para Azure
description: Descreve como avaliar servidores locais para migração para o Azure com a avaliação de servidor de migrações para Azure usando dados importados.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 856f7f7735435579ac14918ee8026f27b222773e
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715505"
---
# <a name="assess-servers-using-imported-data"></a>Avaliar servidores usando dados importados

> [!NOTE]
> Se você ainda não tiver visto esse recurso no portal de migrações para Azure, aguarde. Ele será exibido na próxima semana ou assim.

Este artigo explica como avaliar servidores locais com as [migrações para Azure: avaliação de servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool), importando metadados de servidor usando CSV. Com esse método de avaliação, você não precisa configurar o dispositivo de migrações para Azure para criar uma avaliação. Isso será útil se: 

- Você deseja criar uma avaliação rápida inicial antes de implantar o dispositivo.
- Você não pode implantar o dispositivo de migrações para Azure em sua organização.
- Você não pode compartilhar credenciais que permitam o acesso a servidores locais.
- As restrições de segurança impedem que você colete e envie dados coletados pelo dispositivo para o Azure. Com um arquivo importado, você pode controlar os dados compartilhados, e muitos dados (por exemplo, fornecer endereços IP) são opcionais.


## <a name="before-you-start"></a>Antes de começar

Tenha em atenção que:

- Você pode adicionar até um máximo de 20000 servidores em um único arquivo CSV.
- Você pode adicionar até 20000 servidores em um projeto de migrações para Azure usando CSV.
- Você pode carregar informações do servidor usando o CSV várias vezes para a avaliação do servidor de migrações para Azure.
- Embora a coleta de informações do aplicativo seja útil ao avaliar seu ambiente local para migração, a avaliação do servidor de migrações para Azure não executa atualmente a avaliação no nível do aplicativo e não leva os aplicativos em consideração quando Criando uma avaliação.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure um projeto de migrações para Azure.
> * Preencha um arquivo CSV com as informações do servidor.
> * Importe o arquivo para adicionar informações do servidor à avaliação do servidor de migrações para Azure.
> * Crie e revise uma avaliação.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, revise os artigos de instruções.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="set-azure-permissions-for-azure-migrate"></a>Definir permissões do Azure para migrações para Azure 

Sua conta do Azure precisa de permissões para criar um projeto de migrações para Azure.

1. No portal do Azure, abra a assinatura e selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e clique nela para exibir permissões.
3. Você deve ter permissões de **colaborador** ou de **proprietário** .
    - Se você acabou de criar uma conta gratuita do Azure, você é o proprietário da sua assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.


## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto de migrações para Azure

Configure um novo projeto do Azure Migrate da seguinte forma.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Na **Descrição geral**, em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-assess-import/assess-migrate.png)

4. Em **Introdução**, clique em **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.     
6. Em **detalhes do projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto.

    - [Examine](migrate-support-matrix.md#supported-geographies) as geografias com suporte. A geografia do projeto só é utilizada para armazenar os metadados recolhidos das VMs no local.
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-import/migrate-project.png)


7. Clique em **Seguinte**.
8. Em **selecionar ferramenta de avaliação**, selecione **migrações para Azure: avaliação do servidor** > **Avançar**.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-import/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Analisar + adicionar ferramentas**, analise as definições e clique em **Adicionar ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.


## <a name="prepare-the-csv"></a>Preparar o CSV

Baixe o modelo CSV e adicione informações do servidor a ele.


### <a name="download-the-template"></a>Transferir o modelo

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, clique em **descobrir**.
2. Em **descobrir máquinas**, selecione **importar usando. CSV**.
3. Clique em **baixar** para baixar o. Modelo CSV. Como alternativa, você pode [baixá-lo diretamente](https://go.microsoft.com/fwlink/?linkid=2108404).

    ![Baixar. Modelo CSV](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>Adicionar informações do servidor

Coletar dados do servidor e adicioná-los ao arquivo CSV.

- Para coletar dados, você pode exportá-los de ferramentas usadas para o gerenciamento de servidor local, como VMware vSphere ou o banco de dados de gerenciamento de configuração (CMDB).
- Para examinar os dados de exemplo, baixe nosso [arquivo de exemplo](https://go.microsoft.com/fwlink/?linkid=2108405).


A tabela a seguir resume os campos de arquivo para preencher.

**Nome do campo** | **Obrigatório** | **Detalhes**
--- | --- | ---
**Nome do servidor** | Sim | É recomendável especificar o FQDN. 
**Endereço IP** | Não | Endereço do servidor.
**Número de núcleos** | Sim | O número de núcleos de processador alocados para o servidor.
**Memória** | Sim | Total de RAM (MB) alocada para o servidor.
**Nome do so** | Sim | Sistema operacional do servidor.
**Versão do SO** | Não | Versão do sistema operacional do servidor.
**Número de discos** | Não | Não será necessário se detalhes de disco individuais forem fornecidos.
**Tamanho do disco 1**  | Não | Tamanho máximo do disco (GB)<br/> Você pode adicionar detalhes para mais discos [adicionando colunas](#add-multiple-disks) no modelo. Você pode adicionar até oito discos.
**Operações de leitura de disco 1** | Não | Operações de leitura de disco por segundo.
**Operações de gravação do disco 1** | Não | Operações de gravação de disco por segundo.
**Taxa de transferência de leitura do disco 1** | Não | Dados lidos do disco por segundo em MB por segundo.
**Taxa de transferência de gravação do disco 1** | Não | Dados gravados no disco por segundo em MB por segundo.
**Porcentagem de utilização da CPU** | Não | Percentual de utilização da CPU.
**Porcentagem de utilização da memória** | Não | Porcentagem de utilização de RAM.
**Total de operações de leitura de discos** | Não | Operações de leitura de disco por segundo.
**Total de operações de gravação de discos** | Não | Operações de gravação de disco por segundo.
**Taxa de transferência de leitura do total de discos** | Não | Dados lidos do disco em MB por segundo.
**Taxa de transferência de gravação total de discos** | Não | Dados gravados em disco em MB por segundo.
**Entrada na Rede taxa de transferência** | Não | Dados recebidos pelo servidor em MB por segundo.
**Saída da Rede taxa de transferência** | Não | Dados transmitidos pelo servidor em MB por segundo.
**Tipo de firmware** | Não | Firmware do servidor. Os valores podem ser "BIOS" ou "UEFI"
**Tipo de servidor** | Não | Os valores podem ser "físico" ou "virtual".
**Visor** | Não | Hipervisor no qual um computador está em execução. <br/> Os valores podem ser "VMware", "Hyper-V", "Xen", "AWS", "GCP" ou "other".
**Número de versão do hipervisor** | Não | Versão do hipervisor.
**ID da máquina virtual** | Não | Identificador de VM. Este é o **InstanceUUid** para a VM do VMware vCenter ou a **ID da VM do Hyper-v** para o Hyper-v.
**ID do Virtual Machine Manager** | Não | Este é o **InstanceUUid** para o VMware vCenter. Não é necessário para o Hyper-V.
**Endereço MAC**| Não | Endereço MAC do servidor.
**ID DO BIOS** | Não | ID do BIOS do servidor.
**ID do servidor personalizado**| Não | IDs de servidor locais exclusivas localmente. <br/> Útil para acompanhar o servidor importado pela ID local. 
**Nome do aplicativo 1** | Não | Nome das cargas de trabalho em execução no servidor.<br/> Você pode adicionar detalhes para mais aplicativos [adicionando colunas](#add-multiple-applications) no modelo. Você pode adicionar até cinco aplicativos.
**Tipo de aplicativo 1** | Não | Tipo de carga de trabalho em execução no servidor
**Versão do aplicativo 1** | Não | Versão da carga de trabalho em execução no servidor.
**Expiração da licença do aplicativo 1** | Não | Expiração da licença do para a carga de trabalho (se aplicável).
**Unidade de negócios** | Não | Unidade de negócios à qual o servidor pertence.
**Proprietário da empresa** | Não | Proprietário da unidade de negócios.
**Nome do aplicativo de negócios** | Não | Nome do aplicativo ao qual o aplicativo pertence.
**Localização** | Não | Datacenter no qual o servidor está localizado.
**Data de descomissionamento do servidor** | Não | Data de descomissionamento do servidor físico ou o servidor físico subjacente do servidor virtual

### <a name="add-operating-systems"></a>Adicionar sistemas operacionais

A avaliação reconhece nomes específicos do sistema operacional. Qualquer nome de sistema operacional especificado deve corresponder a uma das opções na lista de [nomes com suporte](#supported-operating-system-names) .


### <a name="add-multiple-disks"></a>Adicionar vários discos

O modelo fornece campos padrão para o primeiro disco.  Você pode adicionar colunas semelhantes para até 8 discos. 

Por exemplo, para especificar todos os campos para um segundo disco, adicione as colunas:

Disco 2 Tamanho disco 2 leitura Ops disco 2 gravação operações disco 2 leitura taxa de transferência disco 2 gravação taxa de transferência

Opcionalmente, você pode adicionar campos específicos apenas para um disco.


### <a name="add-multiple-applications"></a>Adicionar vários aplicativos

O modelo fornece campos para um único aplicativo. Você pode adicionar colunas semelhantes para até cinco aplicativos.  

Por exemplo, para especificar todos os campos para um segundo aplicativo, adicione as colunas:

Aplicativo de nome da aplicação 2, tipo aplicativo 2 versão aplicativo 2 expiração de licença


Opcionalmente, você pode adicionar campos específicos apenas para um aplicativo.

> [!NOTE]
> As informações do aplicativo são úteis ao avaliar seu ambiente local para migração. No entanto, a avaliação do servidor de migrações para Azure não executa atualmente a avaliação no nível do aplicativo e não leva os aplicativos em conta ao criar uma avaliação.


## <a name="upload-the-server-information"></a>Carregar as informações do servidor

Depois de adicionar informações ao modelo CSV, importe os servidores para migrações para Azure: avaliação do servidor.

1. Em migrações para Azure > **descobrir computadores**, navegue até o modelo preenchido.
2. Clique em **importar**.
3. O status de importação é mostrado. 
    - Se os avisos aparecerem no status, você poderá corrigi-los ou continuar sem solucioná-los.
    - Melhorar as informações do servidor conforme sugerido em avisos melhora a precisão da avaliação.
    - Para exibir e corrigir avisos, se eles aparecerem, clique em **baixar detalhes do aviso. CSV**. Isso baixa o CSV, com avisos adicionados. Você pode examinar os avisos e corrigir os problemas conforme necessário. 
    Se forem exibidos erros no status (o status da importação é **falha**), você precisará corrigi-los para poder continuar com a importação. Para fazer isso, baixe o CSV, que agora tem detalhes de erro adicionados. Examine e resolva os erros conforme necessário. Em seguida, carregue o arquivo modificado novamente.
4. Quando o status da importação for **concluído**, as informações do servidor serão importadas.


> [!NOTE]
> Para atualizar as informações do servidor carregadas para migrações para Azure, carregue os dados para o servidor novamente usando o mesmo **nome do servidor**. Observe que o campo **nome do servidor** não pode ser modificado após a importação do modelo. Atualmente, não há suporte para a exclusão de servidores.

## <a name="updating-server-information"></a>Atualizando informações do servidor

Você pode atualizar as informações do servidor carregando os dados para o servidor novamente com o mesmo nome do **servidor**. Não é possível modificar o campo **nome do servidor** . 

Atualmente, não há suporte para a exclusão de servidores.

### <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a descoberta, você pode verificar se os servidores aparecem na portal do Azure.

1. Abra o painel migrações para Azure.
2. Na página **migrações para Azure – servidores** > **migrações para Azure: avaliação do servidor** , clique no ícone que exibe a contagem de **servidores descobertos**.
3. Clique na guia **com base em importar** .

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Há dois tipos de avaliações que você pode criar usando as migrações para Azure: avaliação do servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações com base nos valores de dados de desempenho especificados | **Tamanho de VM recomendado**: com base nos dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: com base no tamanho do servidor especificado<br/><br> **Tipo de disco recomendado**: com base na configuração de tipo de armazenamento que você selecionar para a avaliação.


### <a name="run-an-assessment"></a>Executar uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [práticas recomendadas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **servidores** , no bloco **migrações para Azure: avaliação do servidor** , clique em **avaliar**.

    ![Avaliar](./media/tutorial-assess-physical/assess.png)

2. Em **avaliar servidores**, especifique um nome para a avaliação.
3. Em **origem da descoberta**, selecione **computadores adicionados por meio de importar para migrações para Azure**
3. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades da avaliação](./media/tutorial-assess-physical/view-all.png)

3. Em **selecionar ou criar um grupo**, selecione **criar novo**e especifique um nome de grupo. Um grupo reúne uma ou mais VMs juntas para avaliação.
4. Em **Adicionar computadores ao grupo**, selecione os servidores a serem adicionados ao grupo.
5. Clique em **criar avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-physical/assessment-create.png)

6. Após a criação da avaliação, exiba-a em **servidores** > **migrações para Azure:** **avaliações de > de**avaliação do servidor.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.



## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação do Azure**: se os servidores são adequados para a migração para o Azure.
- **Estimativa de custo mensal**: a computação mensal estimada e os custos de armazenamento para executar os servidores no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para armazenamento em disco após a migração.

### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **metas de migração** >  **servidores**, clique em **avaliações** em **migrações para Azure: avaliação do servidor**.
2. Em **avaliações**, clique em uma avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a preparação do Azure

1. Em **preparação do Azure**, verifique se os servidores estão prontos para a migração para o Azure.
2. Examine o status:
    - **Pronto para o Azure**: as migrações para Azure recomendam um tamanho de VM e estimativas de custo para VMs na avaliação.
    - **Pronto com condições**: mostra problemas e correção sugerida.
    - **Não está pronto para o Azure**: mostra problemas e correção sugerida.
    - **Preparação desconhecida**: usada quando as migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

2. Clique em um status de **preparação do Azure** . Você pode exibir os detalhes de preparação do servidor e fazer uma busca detalhada para ver os detalhes do servidor, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Examinar detalhes de custo

Essa exibição mostra o custo estimado de computação e armazenamento de VMs em execução no Azure.

1. Examine os custos mensais de computação e armazenamento. Os custos são agregados para todos os servidores no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um computador e seus discos e propriedades.
    - Os custos mensais estimados para computação e armazenamento são mostrados.
    - A estimativa de custo é para executar os servidores locais como VMs IaaS. A avaliação do servidor de migrações para Azure não considera os custos de PaaS ou SaaS.

2. Você pode examinar as estimativas de custo de armazenamento mensal. Essa exibição mostra os custos de armazenamento agregados para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.
3. Você pode fazer uma busca detalhada para ver os detalhes de VMs específicas.

> [!NOTE]
> As classificações de confiança não são atribuídas a avaliações de servidores importados para a avaliação do servidor de migrações para Azure usando CSV.


## <a name="supported-operating-system-names"></a>Nomes de sistema operacional com suporte

Nome | Nome
--- | ---
**A-H** | 
Apple Mac OS X 10 | Asianux 3<br/>Asianux 4<br/>Asianux 5
CentOS<br/>CentOS 4/5 | CoreOS Linux 
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD 
**I-R** | 
IBM OS/2 | los |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11 
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | 
**S-T** | 
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7 | Serenity sistemas eComStation 1<br/>Serenity Systems eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** | 
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Limite do Windows Server<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional
    

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Servidores importados para migrações para Azure: avaliação de servidor usando CSV.
> * Criado e revisado uma avaliação

Agora, [implante um dispositivo](./migrate-appliance.md) para obter avaliações mais precisas e reúna servidores em grupos para uma avaliação mais profunda usando a [análise de dependência](./concepts-dependency-visualization.md).
