---
title: Avaliar servidores físicos para migração para o Azure com a avaliação de servidor de migrações para Azure
description: Descreve como avaliar servidores físicos locais para migração para o Azure usando a avaliação de servidor de migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 56b7cc6e95cb85b5508199287cc77aedeebc8b81
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280556"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>Avaliar servidores físicos com as migrações para Azure: avaliação do servidor

Este artigo mostra como avaliar servidores físicos locais, usando a ferramenta migrações para Azure: Server Assessment.

As [migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.

Este tutorial é o segundo de uma série que demonstra como avaliar e migrar servidores físicos para o Azure. Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure um projeto de migrações para Azure.
> * Configure um dispositivo de migrações para Azure que é executado localmente para avaliar servidores físicos.
> * Inicie a descoberta contínua de servidores físicos locais. O dispositivo envia dados de desempenho e configuração de servidores descobertos para o Azure.
> * Agrupe os servidores descobertos e avalie o grupo de servidores.
> * Examine a avaliação.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, revise os artigos de instruções.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Conclua](tutorial-prepare-physical.md) o primeiro tutorial desta série. Caso contrário, as instruções neste tutorial não funcionarão.
- Veja o que você deve ter feito no primeiro tutorial:
    - [Configure as permissões do Azure](tutorial-prepare-physical.md#prepare-azure) para migrações para Azure.
    - [Preparar servidores físicos](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) para avaliação. Os requisitos do dispositivo devem ser verificados. Você também deve ter uma conta configurada para descoberta de servidor físico. As portas necessárias devem estar disponíveis e você deve estar ciente das URLs necessárias para o acesso ao Azure.


## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto de migrações para Azure

Configure um novo projeto do Azure Migrate da seguinte forma.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Na **Descrição geral**, em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-assess-physical/assess-migrate.png)

4. Em **Introdução**, clique em **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.     
6. Em **detalhes do projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto. Há suporte para a Ásia, Europa, Reino Unido e o Estados Unidos.

    - A geografia do projeto é usada somente para armazenar os metadados coletados de servidores locais.
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-physical/migrate-project.png)


7. Clique em **Seguinte**.
8. Em **selecionar ferramenta de avaliação**, selecione **migrações para Azure: avaliação do servidor** > **Avançar**.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-physical/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Analisar + adicionar ferramentas**, analise as definições e clique em **Adicionar ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.


## <a name="set-up-the-appliance"></a>Configurar o dispositivo

Migrações para Azure: a avaliação do servidor executa um dispositivo leve.

- Esse dispositivo executa a descoberta de servidor físico e envia metadados de servidor e dados de desempenho para a avaliação de servidor de migrações para Azure.
- Para configurar o dispositivo, você:
    - Baixe um arquivo compactado com o script do instalador de migrações para Azure do portal do Azure.
    - Extraia o conteúdo do arquivo compactado. Inicie o console do PowerShell com privilégios administrativos.
    - Execute o script do PowerShell para iniciar o aplicativo Web do dispositivo.
    - Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure.
- Você pode configurar vários dispositivos para um único projeto de migrações para Azure. Em todos os dispositivos, você pode descobrir qualquer número de servidores físicos. Um máximo de 250 servidores pode ser descoberto por dispositivo.

### <a name="download-the-installer-script"></a>Baixar o script do instalador

Baixe o arquivo compactado para o dispositivo.

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, clique em **descobrir**.
2. No **Discover machines** > **seus computadores são virtualizados?** , clique em **não virtualizado/outro**.
3. Clique em **baixar** para baixar o arquivo compactado.

    ![Baixar instalador](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Verificar segurança

Verifique se o arquivo compactado é seguro, antes de implantá-lo.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Execute o comando a seguir para gerar o hash para o arquivo compactado
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller\AzureMigrateInstaller.ps1 SHA256```

3.  Para a versão mais recente do dispositivo, o hash gerado deve corresponder a essas configurações.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 96fd99581072c400aa605ab036a0a7c0
  SHA256 | f5454beef510c0aa38ac1c6be6346207c351d5361afa0c9cea4772d566fcdc36

### <a name="run-the-azure-migrate-installer-script"></a>Executar o script de instalador de migrações para Azure

O script do instalador faz o seguinte:

- Instala agentes e um aplicativo Web para avaliação e descoberta de servidor físico.
- Instale as funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o ISE do PowerShell.
- Baixe e instale um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM) com detalhes de configuração persistente para migrações para Azure.
- Cria os seguintes arquivos no caminho:
    - **Arquivos de configuração**:%ProgramData%\Microsoft Azure\Config
    - **Arquivos de log**:%ProgramData%\Microsoft Azure\Logs

Execute o script da seguinte maneira:

1. Extraia o arquivo compactado em uma pasta no servidor que hospedará o dispositivo.
2. Inicie o PowerShell no servidor acima com privilégio administrativo (elevado).
3. Altere o diretório do PowerShell para a pasta em que o conteúdo foi extraído do arquivo compactado baixado.
4. Execute o script chamado **AzureMigrateInstaller. ps1** executando o seguinte comando:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
O script iniciará o aplicativo Web do dispositivo quando ele for concluído com êxito.

No caso de quaisquer problemas, você pode acessar os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log para solucionar problemas.

> [!NOTE]
> Não execute o script do instalador de migrações para Azure em um dispositivo de migrações do Azure existente.

### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se o dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-physical.md#assessment-appliance-url-access).


### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. Abra um navegador em qualquer computador que possa se conectar ao dispositivo e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho clicando no atalho do aplicativo.
2. No aplicativo Web > **configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: o aplicativo verifica se o servidor tem acesso à Internet. Se o servidor usar um proxy:
        - Clique em **configurações de proxy**e especifique o endereço de proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de horário**: o tempo é verificado. O tempo no dispositivo deve ser sincronizado com o horário da Internet para que a descoberta do servidor funcione corretamente.
    - **Instalar atualizações**: avaliação do servidor de migrações para Azure verifica se o dispositivo tem as atualizações mais recentes instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo com as migrações para Azure

1. Clique em **fazer logon**. Se não aparecer, verifique se você desabilitou o bloqueador de pop-ups no navegador.
2. Na nova guia, entre usando suas credenciais do Azure.
    - Entre com seu nome de usuário e senha.
    - Não há suporte para a entrada com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo Web.
4. Selecione a assinatura na qual o projeto de migração do Azure foi criado. Em seguida, selecione o projeto.
5. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique em **registrar**.


## <a name="start-continuous-discovery"></a>Iniciar descoberta contínua

Agora, conecte-se do dispositivo aos servidores físicos a serem descobertos e inicie a descoberta.

1. Clique em **Adicionar credenciais** para especificar as credenciais de conta que o dispositivo usará para descobrir servidores.  
2. Especifique o **sistema operacional**, o nome amigável para as credenciais, o **nome de usuário** e a **senha** e clique em **Adicionar**.
Você pode adicionar um conjunto de credenciais para servidores Windows e Linux.
4. Clique em **Adicionar servidor**e especifique os detalhes do servidor – endereço FQDN/IP e nome amigável das credenciais (uma entrada por linha) para se conectar ao servidor.
3. Clique em **validar**. Após a validação, a lista de servidores que podem ser descobertos é mostrada.
    - Se a validação falhar para um servidor, examine o erro passando o mouse sobre o ícone na coluna **status** . Corrija os problemas e valide novamente.
    - Para remover um servidor, selecione > **excluir**.
4. Após a validação, clique em **salvar e inicie a descoberta** para iniciar o processo de descoberta.

Isso inicia a descoberta. Leva cerca de 1,5 minutos por servidor para que os metadados do servidor descoberto apareçam na portal do Azure.

### <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a descoberta, você pode verificar se os servidores aparecem na portal do Azure.

1. Abra o painel migrações para Azure.
2. Na página **migrações para Azure – servidores** > **migrações para Azure: avaliação do servidor** , clique no ícone que exibe a contagem de **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Há dois tipos de avaliações que você pode criar usando as migrações para Azure: avaliação do servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: com base nos dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: com base no tamanho do servidor local<br/><br> **Tipo de disco recomendado**: com base na configuração de tipo de armazenamento que você selecionar para a avaliação.


### <a name="run-an-assessment"></a>Executar uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [práticas recomendadas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **servidores** , no bloco **migrações para Azure: avaliação do servidor** , clique em **avaliar**.

    ![Avaliar](./media/tutorial-assess-physical/assess.png)

2. Em **avaliar servidores**, especifique um nome para a avaliação.
3. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades da avaliação](./media/tutorial-assess-physical/view-all.png)

3. Em **selecionar ou criar um grupo**, selecione **criar novo**e especifique um nome de grupo. Um grupo reúne um ou mais servidores para avaliação.
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
3. Você pode fazer uma busca detalhada para ver os detalhes de servidores específicos.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando você executa avaliações baseadas em desempenho, uma classificação de confiança é atribuída à avaliação.

![Classificação de confiança](./media/tutorial-assess-physical/confidence-rating.png)

- Uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta) é concedida.
- A classificação de confiança ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança baseia-se na disponibilidade dos pontos de dados necessários para calcular a avaliação.

As classificações de confiança para uma avaliação são as seguintes.

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas

[Saiba mais](best-practices-assessment.md#best-practices-for-confidence-ratings) sobre as práticas recomendadas para classificações de confiança.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar um dispositivo de migrações para Azure
> * Criado e revisado uma avaliação

Continue no terceiro tutorial da série para saber como migrar servidores físicos para o Azure com migrações para Azure: migração de servidor.

> [!div class="nextstepaction"]
> [Migrar servidores físicos](./tutorial-migrate-physical-virtual-machines.md)
