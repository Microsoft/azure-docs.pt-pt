---
title: Avaliar servidores físicos para migração para Azure com avaliação do servidor Azure Migrate
description: Descreve como avaliar servidores físicos no local para migração para Azure usando a Avaliação do Servidor Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: a8a7cc3734bc7a36afc307526cd41634ccaf00dc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933822"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Avaliar servidores físicos com Azure Migrate:Avaliação do servidor

Este artigo mostra-lhe como avaliar servidores físicos no local, utilizando a ferramenta Azure Migrate:Server Assessment.

[A Azure Migrate](migrate-services-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar aplicações, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV).

Este tutorial é o segundo de uma série que demonstra como avaliar e migrar servidores físicos para Azure. Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Crie um projeto Azure Migrate.
> * Crie um aparelho Azure Migrate que funciona no local para avaliar servidores físicos.
> * Inicie a descoberta contínua de servidores físicos no local. O aparelho envia dados de configuração e desempenho para os servidores descobertos para o Azure.
> * O grupo descobriu servidores e avaliou o grupo de servidores.
> * Reveja a avaliação.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos "Como-a-fazer".

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Complete](tutorial-prepare-physical.md) o primeiro tutorial desta série. Se não o fizeres, as instruções deste tutorial não funcionam.
- Eis o que devias ter feito no primeiro tutorial:
    - [Confise Azure](tutorial-prepare-physical.md) para Azure Migrate.
    - [Prepare os servidores físicos](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) para avaliação. Os requisitos do aparelho devem ser verificados. Também deverá ter uma conta configurada para a descoberta do servidor físico. As portas necessárias devem estar disponíveis e deve estar ciente dos URLs necessários para o acesso ao Azure.




## <a name="set-up-an-azure-migrate-project"></a>Criar um projeto Azure Migrate

Configure um novo projeto do Azure Migrate da seguinte forma.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Na **Descrição geral**, em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descubra e avalie servidores](./media/tutorial-assess-physical/assess-migrate.png)

4. Em **Introdução**, clique em **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.  
6. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

    - A geografia do projeto é usada apenas para armazenar os metadados recolhidos a partir de servidores no local.
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-physical/migrate-project.png)


7. Clique em **Seguinte**.
8. Na **ferramenta de avaliação Select**, selecione **Azure Migrate: Avaliação do servidor**Em  >  **seguida**.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-physical/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Rever + adicionar ferramentas,** rever as definições e clicar em Adicionar **ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.


## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o aparelho Azure Migrate

Azure Migrate: A avaliação do servidor executa um aparelho leve.

- Este aparelho realiza a descoberta física do servidor e envia metadados do servidor e dados de desempenho para a Avaliação do Servidor Azure Migrate.
- Para configurar o aparelho:
    - Descarregue um ficheiro com fecho de correr com o script do instalador Azure Migrate a partir do portal Azure.
    - Extrair o conteúdo do ficheiro com fecho. Lançar a consola PowerShell com privilégios administrativos.
    - Execute o script PowerShell para lançar a aplicação web do aparelho.
    - Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.
- Pode configurar vários aparelhos para um único projeto Azure Migrate. Em todos os aparelhos, pode descobrir qualquer número de servidores físicos. Um máximo de 1000 servidores pode ser descoberto por aparelho.

### <a name="generate-the-azure-migrate-project-key"></a>Gere a chave do projeto Azure Migrate

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, select **Discover**.
2. In **Discover machines**  >  **Are your machines virtualized?** **Physical or other (AWS, GCP, Xen, etc.)**
3. Na **tecla de projeto 1:Generate Azure Migrate,** forneça um nome para o aparelho Azure Migrate que irá configurar para a descoberta de servidores físicos ou virtuais. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique na **chave Gerar** para iniciar a criação dos recursos Azure necessários. Por favor, não feche a página das máquinas Discover durante a criação de recursos.
1. Após a criação bem sucedida dos recursos Azure, é gerada uma **chave de projeto Azure Migrate.**
1. Copie a chave pois necessitará para completar o registo do aparelho durante a sua configuração.

### <a name="download-the-installer-script"></a>Descarregue o script do instalador

Em **2: Descarregue o aparelho Azure Migrate,** clique em **Baixar**.

   ![Seleções para máquinas Discover](./media/tutorial-assess-physical/servers-discover.png)


   ![Seleções para Gerar Tecla](./media/tutorial-assess-physical/generate-key-physical.png)


### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Uso de exemplo para nuvem pública: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Uso de exemplo para nuvem governamental: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Verifique as versões mais recentes do aparelho e os valores do haxixe:
    - Para a nuvem pública:

        **Cenário** | **Baixar*** | **Valor de hash**
        --- | --- | ---
        Físico (85 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140334) | 5d0a3dbce4b5010980d59f809acfeb17f5a36f57af4dac4a0a62dde1f

    - Para o Governo de Azure:

        **Cenário** | **Baixar*** | **Valor de hash**
        --- | --- | ---
        Físico (85 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140338) | 1545f9ce8874cedef6347c1a1332f8b5eabd6811a017440a2382525fb0430309

### <a name="run-the-azure-migrate-installer-script"></a>Executar o script do instalador Azure Migrate

O script do instalador faz o seguinte:

- Instala agentes e uma aplicação web para descoberta e avaliação física do servidor.
- Instale funções windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarregue e instale um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM) com detalhes de definição persistente para Azure Migrate.
- Cria os seguintes ficheiros no caminho:
    - **Config Ficheiros**: %Programdata%\Microsoft Azure\Config
    - **Ficheiros de registo**: %Programdata%\Microsoft Azure\Logs

Execute o guião da seguinte forma:

1. Extraia o ficheiro .zip para uma pasta no servidor que irá hospedar o aparelho.  Certifique-se de que não coloca o guião numa máquina num aparelho Azure Migrate existente.
2. Lançar PowerShell no servidor acima com privilégio administrativo (elevado).
3. Mude o diretório PowerShell para a pasta onde o conteúdo foi extraído do ficheiro fechado descarregado.
4. Executar o script nomeado **AzureMigrateInstaller.ps1** executando o seguinte comando:

    - Para a nuvem pública: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Para o Governo de Azure: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    O script lançará a aplicação web do aparelho quando terminar com sucesso.

Se encontrar algum problema, pode aceder aos registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log para resolução de problemas.

### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)


### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

1. Abra um browser em qualquer máquina que possa ligar ao aparelho e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho clicando no atalho da aplicação.
2. Aceite os termos da **licença**e leia as informações de terceiros.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Conectividade**: A aplicação verifica se o servidor tem acesso à Internet. Se o servidor utilizar um representante:
        - Clique em **Configurar o representante** para e especificar o endereço proxy (no formulário http://ProxyIPAddress ou na porta de http://ProxyFQDN) escuta.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
        - Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
    - **Sincronização temporal:** O tempo é verificado. O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta do servidor funcione corretamente.
    - **Instalar atualizações**: A Azure Migrate Server Assessment verifica se o aparelho tem as últimas atualizações instaladas. Depois de concluída a verificação, pode clicar nos **serviços do aparelho para** ver o estado e as versões dos componentes em funcionamento no aparelho.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Cole a chave do **projeto Azure Migrate** copiada do portal. Se não tiver a chave, vá à Avaliação do Servidor> Descubra> Gerir os **aparelhos existentes**, selecione o nome do aparelho que forneceu no momento da geração de chaves e copie a chave correspondente.
1. Clique em **Iniciar sessão**. Abrirá um pedido de login do Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
1. No novo separador, inscreva-se utilizando o seu nome de utilizador E palavra-passe Azure.
   
   O s-in com um PIN não é suportado.
3. Depois de iniciar sessão com sucesso, volte à aplicação web. 
4. Se a conta de utilizador Azure utilizada para a exploração madeireira tiver as [permissões certas](tutorial-prepare-physical.md) sobre os recursos Azure criados durante a geração chave, o registo do aparelho será iniciado.
1. Depois de o aparelho estar registado com sucesso, pode ver os dados do registo clicando nos **detalhes do Ver.**


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Agora, ligue-se do aparelho aos servidores físicos a descobrir e inicie a descoberta.

1. No **Passo 1: Forneça credenciais para a descoberta de servidores físicos ou virtuais do Windows e Linux**, clique em **Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **username** e **Password** para um servidor Windows ou Linux. Clique em **Guardar**.
1. Se quiser adicionar várias credenciais de uma só vez, clique em **Adicionar mais** para guardar e adicionar mais credenciais. Várias credenciais são suportadas para a descoberta de servidores físicos.
1. No **Passo 2:Forneça detalhes físicos ou virtuais do servidor**, clique na fonte de descoberta **Adicionar** para especificar o **endereço IP/FQDN** do servidor e o nome amigável para credenciais para se ligar ao servidor.
1. Pode **adicionar um único item** de cada vez ou adicionar **vários itens** de uma só vez. Existe também uma opção para fornecer detalhes do servidor através **do Import CSV**.

    ![Seleções para adicionar fonte de descoberta](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Se escolher **Adicionar um único item,** pode escolher o tipo de SO, especificar o nome amigável para credenciais, adicionar endereço **IP/FQDN** do servidor e clicar em **Guardar**.
    - Se escolher **Adicionar vários itens,** pode adicionar vários registos ao mesmo tempo especificando **o endereço IP/FQDN** do servidor com o nome amigável para credenciais na caixa de texto. **Verifique** os registos adicionados e clique em **Guardar**.
    - Se escolher **Import CSV** _(selecionado por padrão),_ pode descarregar um ficheiro de modelo CSV, preencher o ficheiro com o endereço **IP/FQDN** do servidor e nomear um nome amigável para credenciais. Em seguida, importa o ficheiro para o aparelho, **verifique** os registos do ficheiro e clique em **Guardar**.

1. Ao clicar em Guardar, o aparelho tentará validar a ligação aos servidores adicionados e mostrar o **estado de Validação** na tabela contra cada servidor.
    - Se a validação falhar para um servidor, reveja o erro clicando na **Validação falhada** na coluna 'Estado' da tabela. Corrija o problema e valide novamente.
    - Para remover um servidor, clique em **Apagar**.
1. Pode **revalidar** a conectividade aos servidores a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar a descoberta**, para iniciar a descoberta dos servidores validados com sucesso. Após a descoberta ter sido iniciada com sucesso, pode verificar o estado de descoberta contra cada servidor da tabela.


Isto começa a ser descoberto. Leva aproximadamente 2 minutos por servidor para que os metadados do servidor descoberto apareçam no portal Azure.

### <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a descoberta, pode verificar se os servidores aparecem no portal Azure.

1. Abra o painel Azure Migrate.
2. Em **Azure Migrate - Servidores**  >  **Azure Migrate:** Página de Avaliação do servidor, clique no ícone que exibe a contagem para **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Existem dois tipos de avaliações que pode criar usando Azure Migrate: Avaliação do servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações com base em dados de desempenho recolhidos | **Tamanho VM recomendado**: Com base em dados de CPU e utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido standard ou premium)**: Baseado no IOPS e na produção dos discos no local.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho VM recomendado**: Com base no tamanho do servidor no local<br/><br> **Tipo de disco recomendado**: Com base na definição do tipo de armazenamento que seleciona para a avaliação.


### <a name="run-an-assessment"></a>Executar uma avaliação

E executar uma avaliação da seguinte forma:

1. Reveja as [melhores práticas](best-practices-assessment.md) para criar avaliações.
2. No separador **Servidores,** em **Azure Migrate: Telha de Avaliação** do Servidor, clique em **Avaliar**.

    ![Avaliar](./media/tutorial-assess-physical/assess.png)

2. Nos **servidores avaliação, especifique**um nome para a avaliação.
3. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-physical/view-all.png)

3. Em **Select ou criar um grupo**, selecione Create **New**, e especifique um nome de grupo. Um grupo reúne um ou mais servidores para avaliação.
4. No **Adicionar máquinas ao grupo,** selecione servidores para adicionar ao grupo.
5. Clique **em Criar Avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-physical/assessment-create.png)

6. Após a avaliação ser criada, veja-a nos **Servidores**  >  **Azure Migrate: Avaliações de Avaliação do Servidor**  >  **Assessments**.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.



## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azul**: Se os servidores são adequados para a migração para Azure.
- **Estimativa mensal dos custos**: Os custos estimados mensalmente de computação e armazenamento para a execução dos servidores em Azure.
- **Estimativa mensal dos custos de armazenamento**: Custos estimados para o armazenamento do disco após a migração.

### <a name="view-an-assessment"></a>Ver uma avaliação

1. Nos **objetivos de migração**  >   **Servidores**, clique em **Avaliações** em **Azure Migrate: Avaliação do servidor**.
2. Em **Avaliações,** clique numa avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Rever prontidão Azure

1. Em **Azure,** verifique se os servidores estão prontos para a migração para Azure.
2. Reveja o estado:
    - **Pronto para Azure**: A azure Migrate recomenda uma estimativa de tamanho e custo de VM para VMs na avaliação.
    - **Pronto com condições**: Apresenta problemas e sugeriu remediação.
    - **Não está pronto para o Azure**: Apresenta problemas e sugeriu a reparação.
    - **Prontidão desconhecida**: Usado quando a Azure Migrate não consegue avaliar a prontidão, devido a problemas de disponibilidade de dados.

2. Clique num estado **de prontidão Azure.** Pode ver os detalhes de prontidão do servidor e aprofundar para ver os detalhes do servidor, incluindo configurações de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Rever detalhes de custos

Esta visão mostra o custo estimado de cálculo e armazenamento de VMs em execução em Azure.

1. Reveja os custos mensais de computação e armazenamento. Os custos são agregados para todos os servidores do grupo avaliado.

    - As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, e nos seus discos e propriedades.
    - São apresentados custos mensais estimados para o cálculo e armazenamento.
    - A estimativa de custos é para executar os servidores no local como IaaS VMs. A avaliação do servidor Azure Migrate não considera os custos do PaaS ou do SaaS.

2. Pode rever as estimativas mensais de custos de armazenamento. Esta visão mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento.
3. Pode perfurar para ver detalhes para servidores específicos.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando faz avaliações baseadas no desempenho, é atribuída uma classificação de confiança à avaliação.

![Classificação de confiança](./media/tutorial-assess-physical/confidence-rating.png)

- É atribuída uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta).
- A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação.

As classificações de confiança para uma avaliação são as seguintes.

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas

[Saiba mais](best-practices-assessment.md#best-practices-for-confidence-ratings) sobre as melhores práticas para as classificações de confiança.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Crie um aparelho Azure Migrate
> * Criei e revendo uma avaliação

Continue até ao terceiro tutorial da série, para aprender a migrar servidores físicos para Azure com Azure Migrate: Server Migration.

> [!div class="nextstepaction"]
> [Migrar servidores físicos](./tutorial-migrate-physical-virtual-machines.md)
