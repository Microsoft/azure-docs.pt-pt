---
title: Avaliar as VMs do Hyper-V para migração para o Azure com migrações para Azure | Microsoft Docs
description: Descreve como avaliar VMs do Hyper-V locais para migração para o Azure usando as migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 08/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b93d9ee65850749e79714b632584f1977ca88c81
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639992"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Avaliar as VMs do Hyper-V com a avaliação do servidor de migrações para Azure

Este artigo mostra como avaliar as VMs do Hyper-V locais, usando as migrações para Azure: Ferramenta de avaliação do servidor.

As migrações para [Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.



Este tutorial é o segundo de uma série que demonstra como avaliar e migrar VMs do Hyper-V para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure um projeto de migrações para Azure.
> * Configurar e registrar um dispositivo de migrações para Azure.
> * Inicie a descoberta contínua de VMs locais.
> * Agrupe as VMs descobertas e avalie o grupo.
> * Examine a avaliação.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, revise os artigos de instruções.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Conclua](tutorial-prepare-hyper-v.md) o primeiro tutorial desta série. Caso contrário, as instruções neste tutorial não funcionarão.
- Veja o que você deve ter feito no primeiro tutorial:
    - [Configure as permissões do Azure](tutorial-prepare-hyper-v.md#prepare-azure) para migrações para Azure.
    - Prepare clusters, hosts e VMs do [Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) para avaliação.

## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto de migrações para Azure

1. Na portal do Azure > **todos os serviços**, procure migrações para **Azure**.
2. Nos resultados da pesquisa, selecione **migrações para Azure**.
3. Em **visão geral**, em **descobrir, avaliar e migrar servidores**, clique em **avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Em **introdução**, clique em **Adicionar ferramentas**.
5. Na guia **migrar projeto** , selecione sua assinatura do Azure e crie um grupo de recursos, se você não tiver um.
6. Em **detalhes do projeto**, especifique o nome do projeto e a região na qual você deseja criar o projeto.


    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-hyper-v/migrate-project.png)

    Você pode criar um projeto de migrações para Azure nessas regiões.

    **Geografia** | **Região**
    --- | ---
    Ásia  | Sudeste Asiático
    Europa | Europa Setentrional ou Europa Ocidental
    Reino Unido |  Sul do Reino Unido ou Oeste do Reino Unido
    Estados Unidos | Leste dos EUA, oeste dos EUA 2 ou Oeste EUA Central

    - A região do projeto é usada somente para armazenar os metadados coletados de VMs locais.
    - Você pode selecionar uma região de destino do Azure diferente ao migrar as VMs. Todas as regiões do Azure têm suporte para o destino de migração.

7. Clique em **Seguinte**.
8. Em **selecionar ferramenta de avaliação**, **selecione migrações para Azure:** Avaliação > do servidor**em seguida**.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. Em **selecionar ferramenta de migração**, selecione **ignorar a adição de uma ferramenta de migração para agora** > **em seguida**.
10. Em **examinar + adicionar ferramentas**, examine as configurações e clique em **Adicionar ferramentas**.
11. Aguarde alguns minutos para que o projeto de migrações para Azure seja implantado. Você será levado para a página do projeto. Se você não vir o projeto, poderá acessá-lo de **servidores** no painel migrações para Azure.




## <a name="set-up-the-appliance-vm"></a>Configurar a VM do dispositivo

A avaliação do servidor de migrações para Azure executa um dispositivo de VM leve do Hyper-V.

- Este dispositivo executa a descoberta de VM e envia dados de desempenho e metadados da VM para migrações para Azure: Avaliação do Servidor.
- Para configurar o dispositivo, você:
    - Baixe um VHD do Hyper-V compactado da portal do Azure.
    - Crie o dispositivo e verifique se ele pode se conectar à avaliação do servidor de migrações para Azure.
    - Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure.

### <a name="download-the-vhd"></a>Baixar o VHD

Baixe o modelo de VHD compactado para o dispositivo.

1. Em **objetivos** > de migração**servidores** > migraçõesparaAzure: **Avaliação**do servidor, clique em **descobrir**.
2. Em **descobrir computadores** > **são seus computadores virtualizados?** , clique em **Sim, com o Hyper-V**.
3. Clique em **baixar** para baixar o arquivo VHD.

    ![Baixar VM](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar segurança

Verifique se o arquivo compactado é seguro, antes de implantá-lo.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.

2. Execute o seguinte comando do PowerShell para gerar o hash para o arquivo ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Para a versão do dispositivo 2.19.07.30, o hash gerado deve corresponder a essas configurações.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | d0a68e76ea24ba4e4a494c0dab95e90e
  SHA256 | 0551221d2a9de75c352c201ccc88f7f10e87e5df1ecda42bfd4ec6c8defc57c0



### <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie a VM.

1. Depois de baixar o arquivo VHD compactado para o host Hyper-V no qual a VM do dispositivo será colocada, extraia o arquivo compactado.
    - No local extraído, o arquivo é descompactado em uma pasta chamada **AzureMigrateAppliance_VersionNumber**.
    - Essa pasta contém uma subpasta, também chamada de **AzureMigrateAppliance_VersionNumber**.
    - Essa subpasta contém três subpastas adicionais– instantâneos, **discos rígidos virtuais**e **máquinas virtuais**.

2. Abra o Gerenciador do Hyper-V. Em **ações**, clique em **importar máquina virtual**.

    ![Implantar VHD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. No assistente de importação de máquina virtual > **antes de começar**, clique em **Avançar**.
3. Em **Localizar pasta**, selecione a pasta **máquinas virtuais** . Clique depois em **Seguinte**.
1. Em **selecionar máquina virtual**, clique em **Avançar**.
2. Em **escolher tipo de importação**, clique em **copiar a máquina virtual (criar uma nova ID exclusiva)** . Clique depois em **Seguinte**.
3. Em **escolher destino**, deixe a configuração padrão. Clique em **Seguinte**.
4. Em **pastas de armazenamento**, deixe a configuração padrão. Clique em **Seguinte**.
5. Em **escolher rede**, especifique o comutador virtual que será usado pela VM. A opção precisa de conectividade com a Internet para enviar dados para o Azure.
6. Em **Resumo**, examine as configurações. Em seguida, clique em **concluir**.
7. No Gerenciador do Hyper-V > **máquinas virtuais**, inicie a VM.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. No Gerenciador do Hyper-V > **máquinas virtuais**, clique com o botão direito do mouse na VM > **conectar**.
2. Forneça o idioma, o fuso horário e a senha para o dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://nome do*dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
1. No aplicativo Web > **configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: Aceite os termos de licenciamento e leia as informações de terceiros.
    - **Conectividade**: O aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
      - Clique em **configurações de proxy**e especifique o endereço de proxy e a porta de escuta http://ProxyIPAddress , http://ProxyFQDN no formato ou.
      - Especifique as credenciais se o proxy precisar de autenticação.
      - Apenas é suportado o proxy HTTP.
    - **Sincronização de horário**: O tempo é verificado. O tempo no dispositivo deve ser sincronizado com o horário da Internet para que a descoberta da VM funcione corretamente.
    - **Instalar atualizações**: A avaliação do servidor de migrações para Azure verifica se o dispositivo tem as atualizações mais recentes instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo com as migrações para Azure

1. Clique em **fazer logon**. Se não aparecer, verifique se você desabilitou o bloqueador de pop-ups no navegador.
2. Na nova guia, entre usando suas credenciais do Azure.
    - Entre com seu nome de usuário e senha.
    - Não há suporte para a entrada com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo Web.
4. Selecione a assinatura na qual o projeto de migração do Azure foi criado. Em seguida, selecione o projeto.
5. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique em **registrar**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegar credenciais para VHDs SMB

Se você estiver executando VHDs em SMBs, deverá habilitar a delegação de credenciais do dispositivo para os hosts do Hyper-V. Isso requer o seguinte:

- Você permite que cada host atue como um delegado para o dispositivo. Você deve ter feito isso no tutorial anterior, quando preparou o Hyper-V para avaliação e migração. Você deve ter configurado o CredSSP para os hosts [manualmente](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)ou [executando o script de configuração de pré-requisitos do Hyper-V](tutorial-prepare-hyper-v.md#hyper-v-prerequisites-configuration-script).
- Habilite a delegação de CredSSP para que o dispositivo de migrações para Azure possa atuar como o cliente, delegando credenciais a um host.

Habilite no dispositivo da seguinte maneira:

#### <a name="option-1"></a>Opção 1

Na VM do dispositivo, execute este comando. HyperVHost1/HyperVHost2 são nomes de host de exemplo.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Exemplo: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Opção 2

Como alternativa, faça isso na Editor de Política de Grupo Local no dispositivo:

1. Em **política** > do computador local**configuração do computador**, clique em **modelos administrativos** > **delegação de credenciais**do**sistema** > .
2. Clique duas vezes em **Permitir Delegação de novas credenciais**e selecione **habilitado**.
3. Em **Opções**, clique em **Mostrar**e adicione cada host Hyper-V que você deseja descobrir à lista, com **WSMan/** como um prefixo.
4. Em seguida, em **delegação de credenciais**, clique duas vezes em **Permitir Delegação de novas credenciais com autenticação de servidor somente NTML**. Novamente, adicione cada host Hyper-V que você deseja descobrir à lista, com **WSMan/** como um prefixo.

## <a name="start-continuous-discovery"></a>Iniciar descoberta contínua

Conecte-se do dispositivo a clusters ou hosts do Hyper-V e inicie a descoberta de VM.

1. Em **nome de usuário** e **senha**, especifique as credenciais de conta que o dispositivo usará para descobrir as VMs. Especifique um nome amigável para as credenciais e clique em **salvar detalhes**.
2. Clique em **Adicionar host**e especifique os detalhes de host/cluster do Hyper-V.
3. Clique em **validar**. Após a validação, o número de VMs que podem ser descobertas em cada host/cluster é mostrado.
    - Se a validação falhar para um host, examine o erro passando o mouse sobre o ícone na coluna **status** . Corrija os problemas e valide novamente.
    - Para remover hosts ou clusters, selecione > **excluir**.
    - Não é possível remover um host específico de um cluster. Você só pode remover o cluster inteiro.
    - Você pode adicionar um cluster, mesmo se houver problemas com hosts específicos no cluster.
4. Após a validação, clique em **salvar e inicie a descoberta** para iniciar o processo de descoberta.

Isso inicia a descoberta. Leva cerca de 15 minutos para que os metadados de VMs descobertas apareçam na portal do Azure.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a conclusão da descoberta, você pode verificar se as VMs aparecem no Portal.

1. Abra o painel migrações para Azure.
2. No **Azure migrar-servidores** > **migrações do Azure: Página avaliação** do servidor, clique no ícone que exibe a contagem de **servidores**descobertos.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Há dois tipos de avaliações que você pode executar usando a avaliação de servidor de migrações para Azure.

**Locação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: Com base nos dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : Com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: Com base no tamanho da VM local<br/><br> **Tipo de disco recomendado**: Com base na configuração de tipo de armazenamento que você selecionar para a avaliação.



### <a name="run-an-assessment"></a>Executar uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [práticas recomendadas](best-practices-assessment.md) para a criação de avaliações.
2. Em **servidores** > migraçõesparaAzure: **Avaliação**do servidor, clique em **avaliar**.

    ![Avaliar](./media/tutorial-assess-hyper-v/assess.png)

3. Em **avaliar servidores**, especifique um nome para a avaliação.
4. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. Em **selecionar ou criar um grupo**, selecione **criar novo** e especifique um nome de grupo. Um grupo reúne uma ou mais VMs juntas para avaliação.
4. Em **Adicionar computadores ao grupo**, selecione VMs para adicionar ao grupo.
5. Clique em **criar avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Após a criação da avaliação, exiba-a nos **servidores** > **migrações para Azure: Avaliação**do servidor.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.


## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação do Azure**: Se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: A computação mensal estimada e os custos de armazenamento para executar as VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: Custos estimados para o armazenamento em disco após a migração.


### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **objetivos** >  de migração**servidores** > migraçõesparaAzure: **Avaliação**do servidor, clique em **avaliações**.
2. Em **avaliações**, clique em uma avaliação para abri-la.

    ![Resumo de avaliação](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Examinar a preparação do Azure

1. Em **preparação do Azure**, verifique se as VMs estão prontas para migração para o Azure.
2. Examine o status da VM:
    - **Pronto para o Azure**: As migrações para Azure recomendam um tamanho de VM e estimativas de custo para VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e correção sugerida.
    - **Não está pronto para o Azure**: Mostra problemas e correção sugerida.
    - **Preparação desconhecida**: Usado quando as migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

2. Clique em um status de **preparação do Azure** . Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Examinar detalhes de custo

Essa exibição mostra o custo estimado de computação e armazenamento de VMs em execução no Azure.

1. Examine os custos mensais de computação e armazenamento. Os custos são agregados para todas as VMs no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um computador e seus discos e propriedades.
    - Os custos mensais estimados para computação e armazenamento são mostrados.
    - A estimativa de custo é para executar as VMs locais como VMs de IaaS. A avaliação do servidor de migrações para Azure não considera os custos de PaaS ou SaaS.

2. Você pode examinar as estimativas de custo de armazenamento mensal. Essa exibição mostra os custos de armazenamento agregados para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.
3. Você pode fazer uma busca detalhada para ver os detalhes de VMs específicas.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando você executa avaliações baseadas em desempenho, uma classificação de confiança é atribuída à avaliação.

![Classificação de confiança](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Continue no terceiro tutorial da série para saber como migrar VMs do Hyper-V para o Azure com a migração de servidor de migrações para Azure.

> [!div class="nextstepaction"]
> [Migrar VMs do Hyper-V](./tutorial-migrate-hyper-v.md)
