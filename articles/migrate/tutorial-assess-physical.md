---
title: Avaliar servidores físicos para migração para Azure com avaliação do servidor migratório Azure
description: Descreve como avaliar os servidores físicos no local para migração para Azure usando a Avaliação do Servidor Migratório Azure.
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: c89c731712a625e5f3b7a1a7e9306f6a7480b96b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76990305"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>Avaliar servidores físicos com Azure Migrate: Avaliação do Servidor

Este artigo mostra-lhe como avaliar os servidores físicos no local, utilizando a ferramenta De avaliação do servidor Azure Migrate: Server.

[A Azure Migrate](migrate-services-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV).

Este tutorial é o segundo de uma série que demonstra como avaliar e migrar servidores físicos para O Azure. Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um projeto Azure Migrate.
> * Instale um aparelho Azure Migrate que funciona no local para avaliar servidores físicos.
> * Inicie a descoberta contínua de servidores físicos no local. O aparelho envia dados de configuração e desempenho para servidores descobertos para o Azure.
> * O grupo descobriu servidores e avaliou o grupo de servidores.
> * Reveja a avaliação.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa rapidamente configurar uma prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos como fazer.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Complete](tutorial-prepare-physical.md) o primeiro tutorial desta série. Se não o fizeres, as instruções neste tutorial não funcionarão.
- Eis o que deviater feito no primeiro tutorial:
    - [Instale permissões Azure](tutorial-prepare-physical.md#prepare-azure) para a Migração Azure.
    - [Prepare servidores físicos](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) para avaliação. Os requisitos do aparelho devem ser verificados. Também deve ter uma conta configurada para a descoberta do servidor físico. As portas necessárias devem estar disponíveis e deve estar ciente dos URLs necessários para o acesso ao Azure.


## <a name="set-up-an-azure-migrate-project"></a>Criar um projeto Azure Migrate

Configure um novo projeto do Azure Migrate da seguinte forma.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Na **Descrição geral**, em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descubra e avalie servidores](./media/tutorial-assess-physical/assess-migrate.png)

4. Em **Introdução**, clique em **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.     
6. Em Detalhes do **Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. A Ásia, a Europa, o Reino Unido e os Estados Unidos são apoiados.

    - A geografia do projeto é usada apenas para armazenar os metadados recolhidos a partir de servidores no local.
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-physical/migrate-project.png)


7. Clique em **Seguinte**.
8. Na **ferramenta de avaliação Select,** selecione **Azure Migrate: Server Assessment** > **Next**.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-physical/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Rever + adicionar ferramentas,** reveja as definições e clique em Adicionar **ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.


## <a name="set-up-the-appliance"></a>Instale o aparelho

Azure Migrate: A avaliação do servidor executa um aparelho leve.

- Este aparelho realiza a descoberta física do servidor e envia metadados do servidor e dados de desempenho para a Avaliação do Servidor Migratório Azure.
- Para configurar o aparelho:
    - Descarregue um ficheiro com fecho com o script do instalador Azure Migrate a partir do portal Azure.
    - Extrair o conteúdo do ficheiro com fecho. Lance a consola PowerShell com privilégios administrativos.
    - Execute o script PowerShell para lançar a aplicação web do aparelho.
    - Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.
- Pode configurar vários aparelhos para um único projeto Azure Migrate. Em todos os aparelhos, pode descobrir vários servidores físicos. Um máximo de 250 servidores pode ser descoberto por aparelho.

### <a name="download-the-installer-script"></a>Descarregue o script do instalador

Descarregue o ficheiro zipped para o aparelho.

1. Em **Objetivos** > de Migração**Servidores** > **Azure Migrar: Avaliação do servidor,** clique **em Descobrir**.
2. Em **Discover machines** > **As suas máquinas estão virtualizadas?** **Not virtualized/Other**
3. Clique em **Baixar** para descarregar o ficheiro zipped.

    ![Descarregue o instalador](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o ficheiro com fecho está seguro, antes de o implantar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3.  Para a versão mais recente do aparelho, o hash gerado deve coincidir com estas definições.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 1e92ede3e87c03bd148e56a708cd33f
  SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-azure-migrate-installer-script"></a>Executar o script de instalação Azure Migrate

O script do instalador faz o seguinte:

- Instala agentes e uma aplicação web para descoberta e avaliação de servidores físicos.
- Instale funções windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarregue e instale um módulo rewritável IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM) com detalhes de definição persistentes para o Azure Migrate.
- Cria os seguintes ficheiros no caminho:
    - **Ficheiros Config**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de Registo**: %ProgramData%\Microsoft Azure\Logs

Executar o guião da seguinte forma:

1. Extraio o ficheiro com fecho numa pasta no servidor que irá alojar o aparelho.
2. Lance powerShell no servidor acima com privilégio administrativo (elevado).
3. Mude o diretório PowerShell para a pasta onde os conteúdos foram extraídos do ficheiro com fecho descarregado.
4. Executar o script chamado **AzureMigrateInstaller.ps1** executando o seguinte comando:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
O script lançará a aplicação web do aparelho quando terminar com sucesso.

Em caso de problemas, pode aceder aos registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log for troubleshooting.

> [!NOTE]
> Por favor, não execute o script do instalador Azure Migrate num aparelho de migração Azure existente.

### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho pode ligar-se a [URLs Azure](migrate-appliance.md#url-access).


### <a name="configure-the-appliance"></a>Configure o aparelho

Instale o aparelho pela primeira vez.

1. Abra um navegador em qualquer máquina que possa ligar-se ao aparelho e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho clicando no atalho da aplicação.
2. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o servidor tem acesso à Internet. Se o servidor utilizar um proxy:
        - Clique em **definições proxy**, e especifique o endereço proxy e a porta de escuta, no formulário http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de tempo**: O tempo é verificado. O tempo no aparelho deve estar sincronizado com o tempo de internet para que a descoberta do servidor funcione corretamente.
    - **Instalar atualizações**: A Avaliação do Servidor Migratório Azure verifica se o aparelho tem as últimas atualizações instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Clique **em iniciar sessão**. Se não aparecer, certifique-se de que desativou o bloqueador pop-up no navegador.
2. No novo separador, inscreva-se utilizando as suas credenciais Azure.
    - Inscreva-se com o seu nome de utilizador e senha.
    - O sessão com um PIN não é suportado.
3. Depois de iniciar sessão com sucesso, volte para a aplicação web.
4. Selecione a subscrição em que foi criado o projeto Azure Migrate. Em seguida, selecione o projeto.
5. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique no **Registo**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Agora, ligue-se do aparelho aos servidores físicos a serem descobertos e inicie a descoberta.

1. Clique em **Adicionar Credenciais** para especificar as credenciais de conta que o aparelho utilizará para descobrir servidores.  
2. Especifique o **Sistema Operativo**, nome amigável para as credenciais, **nome de utilizador** e **palavra-passe** e clique em **Adicionar**.
Pode adicionar um conjunto de credenciais cada um para servidores Windows e Linux.
4. Clique em **Adicionar servidor**, e especifique os detalhes do servidor - endereço FQDN/IP e nome amigável de credenciais (uma entrada por linha) para ligar ao servidor.
3. Clique em **Validar**. Após validação, a lista de servidores que podem ser descobertos é mostrada.
    - Se a validação falhar num servidor, reveja o erro pairando sobre o ícone na coluna **'Estado'.** Corrija os problemas e valide novamente.
    - Para remover um servidor, selecione > **Eliminar**.
4. Após validação, clique em **Guardar e iniciar** a descoberta para iniciar o processo de descoberta.

Isto começa a ser descoberto. Leva cerca de 1,5 minutos por servidor para que os metadados do servidor descoberto apareçam no portal Azure.

### <a name="verify-servers-in-the-portal"></a>Verifique os servidores no portal

Após a descoberta, pode verificar se os servidores aparecem no portal Azure.

1. Abra o painel de migração Azure.
2. No **Azure Migrate - Servers** > **Azure Migrar:** Página de Avaliação do Servidor, clique no ícone que apresenta a contagem para **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Existem dois tipos de avaliações que pode criar usando o Azure Migrate: Server Assessment.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações baseadas em dados de desempenho recolhidos | **Tamanho VM recomendado**: Baseado em CPU e dados de utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido padrão ou premium)**: Baseado no IOPS e na entrada dos discos no local.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho VM recomendado**: Baseado no tamanho do servidor no local<br/><br> **Tipo**de disco recomendado : Com base na definição do tipo de armazenamento selecionada para a avaliação.


### <a name="run-an-assessment"></a>Executar uma avaliação

Eexecutar uma avaliação da seguinte forma:

1. Reveja as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. No separador **Servidores,** em **Azure Migrate: Server Assessment** tile, clique em **Avaliar**.

    ![Avaliar](./media/tutorial-assess-physical/assess.png)

2. Em **avaliar os servidores,** especifique um nome para a avaliação.
3. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-physical/view-all.png)

3. Em **Selecionar ou criar um grupo,** selecione Criar **Novo**e especifique um nome de grupo. Um grupo reúne um ou mais servidores para avaliação.
4. Em **Adicionar máquinas ao grupo,** selecione servidores para adicionar ao grupo.
5. Clique em **Criar Avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-physical/assessment-create.png)

6. Após a criação da avaliação, veja-a em **Servers** > **Azure Migrate:** > **Avaliações**de Avaliação do Servidor .
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.



## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azure**: Se os servidores são adequados para migração para Azure.
- **Estimativa mensal de custos**: A estimativa mensal de cálculo e armazenamento para a execução dos servidores em Azure.
- **Estimativa mensal**do custo do armazenamento : Custos estimados para armazenamento de disco após migração.

### <a name="view-an-assessment"></a>Ver uma avaliação

1. Nos **objetivos** >  de migração**Servidores**, clique em **Avaliações** em **Migração Azure: Avaliação do Servidor**.
2. Em **Avaliações,** clique numa avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Rever a prontidão do Azure

1. Na **prontidão do Azure,** verifique se os servidores estão prontos para a migração para O Azure.
2. Reveja o estado:
    - **Pronto para Azure**: A Azure Migrate recomenda uma estimativa de tamanho VM e custos para os VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e sugeriu reparação.
    - **Não está pronto para o Azure**: Mostra questões e sugeriu reparação.
    - **Prontidão desconhecida**: Usado quando o Azure Migrate não consegue avaliar a prontidão, devido a problemas de disponibilidade de dados.

2. Clique num estado de **prontidão Azure.** Pode ver os detalhes de prontidão do servidor e perfurar para ver os detalhes do servidor, incluindo definições de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Analisar detalhes de custos

Esta visão mostra o cálculo estimado e o custo de armazenamento de vMs de funcionamento em Azure.

1. Reveja os custos mensais de cálculo e armazenamento. Os custos são agregados para todos os servidores do grupo avaliado.

    - As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, e nos seus discos e propriedades.
    - Os custos mensais estimados para a computação e armazenamento são apresentados.
    - A estimativa de custos é para executar os servidores no local como VMs IaaS. A Avaliação do Servidor Migratório Azure não considera os custos do PaaS ou do SaaS.

2. Pode rever as estimativas mensais de custos de armazenamento. Esta vista mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento.
3. Pode perfurar para ver detalhes para servidores específicos.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando executa avaliações baseadas no desempenho, uma classificação de confiança é atribuída à avaliação.

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

[Saiba mais](best-practices-assessment.md#best-practices-for-confidence-ratings) sobre as melhores práticas para avaliações de confiança.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Instale um aparelho Azure Migrate
> * Criou e reviu uma avaliação

Continue até ao terceiro tutorial da série, para aprender a migrar servidores físicos para Azure com Azure Migrate: Server Migration.

> [!div class="nextstepaction"]
> [Migrar servidores físicos](./tutorial-migrate-physical-virtual-machines.md)
