---
title: Crie um aparelho Azure Migrate para Hiper-V
description: Saiba como configurar um aparelho Azure Migrate para avaliar e migrar servidores em Hiper-V.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 71fe30212b31e810bfe3e1ba10f80be6b09ad4fc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863688"
---
# <a name="set-up-an-appliance-for-servers-on-hyper-v"></a>Configurar um aparelho para servidores em Hiper-V

Siga este artigo para configurar o aparelho Azure Migrate para a descoberta e avaliação dos servidores em Hiper-V com a ferramenta [Azure Migrate: Discovery and assessment.](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)

O [aparelho Azure Migrate](migrate-appliance.md)  é um aparelho leve utilizado pela Azure Migrate: Discovery and assessment/ Migration para descobrir servidores no local em Hiper-V e enviar metadados/dados de desempenho do servidor para o Azure.

Pode utilizar o aparelho utilizando um par de métodos:

- Instale-se num servidor em Hyper-V usando um VHD descarregado. Este método descrito neste artigo.
- Configurar num servidor em Hiper-V ou servidor físico com um script instalador PowerShell. [Este método](deploy-appliance-script.md) deve ser utilizado se não puder configurar um servidor usando um VHD, ou se estiver no Governo Azure.

Depois de criar o aparelho, verifique se pode ligar-se ao Azure Migrate: Discovery and assessment, configure-o pela primeira vez e registá-lo com o projeto.

## <a name="appliance-deployment-vhd"></a>Implantação do aparelho (VHD)

Para configurar o aparelho utilizando um modelo VHD:

- Forneça um nome de aparelho e gere uma chave de projeto no portal.
- Faça o download de um Hiper-VHD comprimido do portal Azure.
- Crie o aparelho e verifique se pode ligar-se ao Azure Migrate: Descoberta e avaliação.
- Configure o aparelho pela primeira vez e registe-o com o projeto utilizando a chave do projeto.

### <a name="generate-the-project-key"></a>Gerar a chave do projeto

1. Em **Objetivos de Migração**  >  **Windows, Linux e SQL Servers**  >  **Azure Migrate: Discovery and assessment**, selecione **Discover**.
2. In **Discover servers**  >  **Are your servers virtualized?** 
3. Em **1:Gerar tecla de projeto,** forneça um nome para o aparelho Azure Migrate que irá configurar para a descoberta de servidores em Hiper-V.O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique na **chave Gerar** para iniciar a criação dos recursos Azure necessários. Não feche a página de servidores Discover durante a criação de recursos.
1. Após a criação bem sucedida dos recursos Azure, gera-se uma **chave de projeto.**
1. Copie a chave pois necessitará para completar o registo do aparelho durante a sua configuração.

### <a name="download-the-vhd"></a>Descarregue o VHD

Em **2: Descarregue o aparelho Azure Migrate,** selecione o . Ficheiro VHD e clique no **Download**.

   ![Seleções para servidores Discover](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Seleções para Gerar Tecla](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No servidor para o qual descarregou o ficheiro, abra uma janela de comando do administrador.
2. Executar o seguinte comando para gerar o haxixe para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance"></a>Criar o aparelho

Importe o ficheiro descarregado e crie um aparelho.

1. Extraia o ficheiro VHD com fecho para uma pasta no hospedeiro Hyper-V que irá hospedar o aparelho. Três pastas são extraídas.
2. Abra o Gestor de Hyper-V. Em **Ações,** clique em **Import Virtual Machine**.

    ![Implementar VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. No > do assistente de máquina virtual de importação **Antes de começar,** clique em **seguida**.
3. Na **Pasta Localizar**, especifique a pasta que contém o VHD extraído. Em seguida, clique em **Seguinte**.
1. Na **Máquina Virtual Selecionada,** clique em **Seguinte**.
2. No **Choose Import Type,** clique **em Copiar a máquina virtual (crie um novo ID único)**. Em seguida, clique em **Seguinte**.
3. No **Destino Escolha,** deixe a definição predefinida. Clique em **Seguinte**.
4. Nas **pastas de armazenamento,** deixe a definição predefinida. Clique em **Seguinte**.
5. In **Choose Network**, especifique o interruptor virtual que o servidor irá utilizar. O switch precisa de conectividade na Internet para enviar dados para o Azure.
6. Em **Resumo,** reveja as definições. Em seguida, clique em **Concluir**.
7. No Hyper-V Manager > **Máquinas Virtuais,** inicie o VM.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)

### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [script PowerShell](deploy-appliance-script.md) em vez do VHD descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. No Hyper-V Manager > **Máquinas Virtuais,** clique com o botão direito no servidor > **Connect**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer sistema que possa ligar ao aparelho e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço *IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho clicando no atalho da aplicação.
1. Aceite os termos da **licença** e leia as informações de terceiros.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Conectividade**: A aplicação verifica se o servidor tem acesso à Internet. Se o servidor utilizar um representante:
      - Clique no **proxy configurar** e especifique o endereço de procuração (no formulário http://ProxyIPAddress ou na porta de http://ProxyFQDN) audição.
      - Especifique as credenciais se o proxy precisar de autenticação.
      - Apenas é suportado o proxy HTTP.
      - Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
    - **Sincronização temporal:** O tempo é verificado. O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta do servidor funcione corretamente.
    - **Instalar atualizações**: Azure Migrate: A descoberta e avaliação verificam se o aparelho tem as últimas atualizações instaladas. Depois de concluída a verificação, pode clicar nos **serviços do aparelho para** ver o estado e as versões dos componentes em funcionamento no aparelho.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Cole a chave do **projeto** copiada do portal. Se não tiver a chave, vá ao **Azure Migrate: Descoberta e avaliação> Descubra> Gerir os aparelhos existentes**, selecione o nome do aparelho que forneceu no momento da geração chave e copie a chave correspondente.
1. Necessitará de um código de dispositivo para autenticar com o Azure. Clicar no **Login** abrirá um código modal com o código do dispositivo, como mostrado abaixo.

    ![Modal mostrando o código do dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Clique no **código copy & Iniciar sessão** para copiar o código do dispositivo e abrir um pedido de Login Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
1. No novo separador, cole o código do dispositivo e inscreva-se utilizando o seu nome de utilizador Estaure e a palavra-passe.
   
   O s-in com um PIN não é suportado.
3. Caso feche o separador de login acidentalmente sem iniciar sessão, é necessário atualizar o separador de navegador do gestor de configuração do aparelho para ativar novamente o botão Iniciar sessão.
1. Depois de iniciar sessão com sucesso, volte ao separador anterior com o gestor de configuração do aparelho.
4. Se a conta de utilizador Azure utilizada para a exploração madeireira tiver as [permissões certas](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) sobre os recursos Azure criados durante a geração chave, o registo do aparelho será iniciado.
1. Depois de o aparelho estar registado com sucesso, pode ver os dados do registo clicando nos **detalhes do Ver.**



### <a name="delegate-credentials-for-smb-vhds"></a>Credenciais de delegado para VHDs SMB

Se estiver a executar VHDs em SMBs, deve ativar a delegação de credenciais do aparelho para os anfitriões Hiper-V. Para o fazer a partir do aparelho:

1. No aparelho, este comando. HyperVHost1/HyperVHost2 são nomes de anfitriões exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Em alternativa, faça-o no Editor de Política do Grupo Local sobre o aparelho:
    - Na   >  **configuração** do computador local, clique na delegação de credenciais **de sistema de modelos**  >    >  **administrativos**.
    - Clique duas **vezes Em permitir a delegação de credenciais frescas** e selecione **Ativado**.
    - Em **Opções**, clique em **Mostrar**, e adicione cada anfitrião Hyper-V que pretende descobrir na lista, com **wsman/** como prefixo.
    - Na  **Delegação de Credenciais,** clique duplo **Deixe delegar credenciais frescas com autenticação do servidor apenas NTLM**. Mais uma vez, adicione cada anfitrião Hyper-V que pretende descobrir na lista, com **wsman/** como prefixo.

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Ligue do aparelho a anfitriões ou agrupamentos Hiper-V e inicie a descoberta.

1. No **passo 1: Forneça credenciais de anfitrião Hyper-V**, clique em **Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **nome de utilizador** e **palavra-passe** para um anfitrião/cluster Hyper-V que o aparelho utilizará para descobrir servidores. Clique em **Guardar**.
1. Se quiser adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para guardar e adicionar mais credenciais. Várias credenciais são suportadas para a descoberta de servidores em Hiper-V.
1. No **passo 2: Forneça detalhes do anfitrião/cluster hyper-V,** clique na **fonte de descoberta Add** para especificar o endereço **IP/cluster/FQDN** do hiper-V e o nome amigável para credenciais de ligação ao hospedeiro/cluster.
1. Pode **adicionar um único item** de cada vez ou adicionar **vários itens** de uma só vez. Existe também uma opção para fornecer detalhes de anfitrião/cluster Hiper-V através **do Import CSV**.

    ![Seleções para adicionar fonte de descoberta](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Se escolher **Adicionar um único item,** tem de especificar o nome amigável para credenciais e o endereço **IP/cluster/FQDN** do anfitrião Hiper-V/cluster e clicar em **Guardar**.
    - Se escolher **Adicionar vários itens** _(selecionados por padrão)_, pode adicionar vários registos ao mesmo tempo, especificando **o endereço IP/cluster/FQDN** do Hiper-V com o nome amigável para credenciais na caixa de texto. Verifique** os registos adicionados e clique em **Guardar**.
    - Se escolher **O CSV de Importação,** pode descarregar um ficheiro de modelo CSV, preencher o ficheiro com o **endereço IP/FQDN** do anfitrião Hiper-V/cluster e nome amigável para credenciais. Em seguida, importa o ficheiro para o aparelho, **verifique** os registos do ficheiro e clique em **Guardar**.

1. Ao clicar em Guardar, o aparelho tentará validar a ligação aos anfitriões/clusters hiper-V adicionados e mostrar o estado de **Validação** na tabela contra cada hospedeiro/cluster.
    - Para anfitriões/clusters validados com sucesso, pode ver mais detalhes clicando no seu endereço IP/FQDN.
    - Se a validação falhar para um anfitrião, reveja o erro clicando na **Validação falhada** na coluna Status da tabela. Corrija o problema e valide novamente.
    - Para remover anfitriões ou agrupamentos, clique em **Apagar**.
    - Não se pode remover um hospedeiro específico de um aglomerado. Só se pode remover todo o aglomerado.
    - Pode adicionar um cluster, mesmo que existam problemas com anfitriões específicos no cluster.
1. Pode **revalidar** a conectividade aos anfitriões/agrupamentos a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar a descoberta**, para iniciar a descoberta do servidor a partir dos anfitriões/clusters validados com sucesso. Após a descoberta ter sido iniciada com sucesso, pode verificar o estado de descoberta contra cada hospedeiro/cluster na tabela.

Isto começa a ser descoberto. Leva aproximadamente 2 minutos por anfitrião para que os metadados dos servidores descobertos apareçam no portal Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após o fim da descoberta, pode verificar se os servidores aparecem no portal.

1. Abra o dashboard do Azure Migrate.
2. Em **Azure Migrate - Windows, Linux e SQL Servers**  >  **Azure Migrate: Página de descoberta e avaliação,** clique no ícone que exibe a contagem para **servidores Descobertos**.

## <a name="next-steps"></a>Passos seguintes

Experimente [a avaliação do Hiper-V](tutorial-assess-hyper-v.md) com a Azure Migrate: Descoberta e avaliação.