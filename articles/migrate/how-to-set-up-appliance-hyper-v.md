---
title: Instale um aparelho Azure Migrate para Hyper-V
description: Aprenda a montar um aparelho Azure Migrate para avaliar e migrar VMs Hiper-V.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 77c13a3a8c87d116bd0863324d28669185c53c84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538295"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurar um aparelho para VMs hiper-V

Siga este artigo para configurar o aparelho Azure Migrate para avaliação de VMs Hiper-V com a ferramenta de avaliação de [migração Azure:Servidor.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

O [aparelho Azure Migrate](migrate-appliance.md) é um aparelho leve utilizado pela Azure Migrate:Server Assessment/Migration para descobrir no local hiper-V V Ms e enviar metadados/dados de desempenho VM para o Azure.

Pode utilizar o aparelho utilizando um par de métodos:

- Instale-se num VM Hiper-V utilizando um VHD descarregado. Este é o método descrito neste artigo.
- Instale-se num VM Hiper-V ou numa máquina física com um script de instalação PowerShell. [Este método](deploy-appliance-script.md) deve ser usado se não conseguir configurar um VM usando um VHD, ou se estiver no Governo Azure.

Depois de criar o aparelho, verifique se pode ligar-se à Avaliação do Servidor Azure Migrate:Server, configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

## <a name="appliance-deployment-vhd"></a>Implantação de aparelhos (VHD)

Para configurar o aparelho com um modelo VHD:

- Baixe um VHD Hiper-V comprimido a partir do portal Azure.
- Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Migratório Azure.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.

## <a name="download-the-vhd"></a>Descarregue o VHD

Descarregue o modelo VHD com fecho para o aparelho.

1. Em **Objetivos** > de Migração**Servidores** > **Azure Migrar: Avaliação do servidor,** clique **em Descobrir**.
2. Em **Discover machines** > **As suas máquinas estão virtualizadas?** **Yes, with Hyper-V**
3. Clique em **Baixar** para descarregar o ficheiro VHD.

    ![Baixar VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o ficheiro com fecho está seguro, antes de o implantar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  Para a versão do aparelho 2.19.11.12, o hash gerado deve coincidir com estas [definições](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security).




## <a name="create-the-appliance-vm"></a>Crie o vm do aparelho

Importe o ficheiro descarregado e crie o VM.

1. Extraio o ficheiro VHD com fecho numa pasta no hospedeiro Hyper-V que irá alojar o VM do aparelho. Três pastas são extraídas.
2. Abra o Gestor de Hyper-V. Em **Ações,** clique **em Importar Máquina Virtual**.

    ![Implementar VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. No > de Máquina Virtual importar antes de **começar,** clique em **Seguinte**.
3. Na **pasta localizar,** especifique a pasta que contém o VHD extraído. Em seguida, clique em **Seguinte**.
1. Na **selecione Máquina Virtual,** clique em **Seguinte**.
2. Em **Escolher o Tipo de Importação,** clique em copiar a máquina virtual **(criar um novo ID único)**. Em seguida, clique em **Seguinte**.
3. No **Destino Escolha,** deixe a definição predefinida. Clique em **Seguinte**.
4. Nas **pastas de armazenamento,** deixe a definição predefinida. Clique em **Seguinte**.
5. Na **Rede Escolha,** especifique o interruptor virtual que o VM utilizará. O interruptor precisa de conectividade de internet para enviar dados para o Azure.
6. Em **resumo,** reveja as definições. Em seguida, clique em **Concluir**.
7. Em Hyper-V Manager > **Máquinas Virtuais,** inicie o VM.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o vM do aparelho pode ligar-se a URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)

## <a name="configure-the-appliance"></a>Configure o aparelho

Instale o aparelho pela primeira vez. Se utilizar o aparelho utilizando um script em vez de um VHD, os dois primeiros passos do procedimento não são aplicáveis.

1. Em Hyper-V Manager > **Máquinas Virtuais,** clique à direita no VM > **Connect**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um navegador em qualquer máquina que possa ligar-se ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho clicando no atalho da aplicação.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um proxy:
        - Clique em **definições proxy**, e especifique o endereço proxy e a porta de escuta, no formulário http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de tempo**: O tempo é verificado. O tempo no aparelho deve estar sincronizado com o tempo de internet para que a descoberta de VM funcione corretamente.
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


### <a name="delegate-credentials-for-smb-vhds"></a>Credenciais de delegado para VHDs SMB

Se estiver a executar VHDs em SMBs, deve ativar a delegação de credenciais do aparelho para os anfitriões Hyper-V. Para fazer isto a partir do aparelho:

1. No vM do aparelho, execute este comando. HyperVHost1/HyperVHost2 são nomes de anfitriões exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Em alternativa, faça-o no Editor de Política do Grupo Local sobre o aparelho:
    - Na**configuração**do computador de **política** > de computador local, clique em**credenciais**de**credenciais** > do sistema de **modelos administrativos** > .
    - Clique duplo **Para permitir a delegação**de novas credenciais e selecionar **Ativado**.
    - Em **Opções,** clique em **Mostrar**, e adicione cada anfitrião Hyper-V que deseja descobrir na lista, com **wsman/** como prefixo.
    - Na **Delegação de Credenciais,** clique duplo **Permita delegar novas credenciais com autenticação de servidor apenas NTLM**. Mais uma vez, adicione cada anfitrião Hyper-V que deseja descobrir na lista, com **wsman/** como prefixo.

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Ligue-se do aparelho aos anfitriões ou aglomerados hyper-V e inicie a descoberta vm.

1. No **nome do utilizador** e na **palavra-passe,** especifique as credenciais de conta que o aparelho utilizará para descobrir VMs. Especifique um nome amigável para as credenciais e clique em **Guardar detalhes**.
2. Clique em **Adicionar anfitrião**e especificar detalhes do hospedeiro/cluster hyper-V.
3. Clique em **Validar**. Após validação, é mostrado o número de VMs que podem ser descobertos em cada hospedeiro/cluster.
    - Se a validação falhar para um hospedeiro, reveja o erro pairando sobre o ícone na coluna **'Estado'.** Corrija os problemas e valide novamente.
    - Para remover hospedeiros ou agrupamentos, selecione > **Eliminar**.
    - Não se pode remover um hospedeiro específico de um aglomerado. Só se pode remover todo o aglomerado.
    - Pode adicionar um cluster, mesmo que existam problemas com anfitriões específicos no cluster.
4. Após validação, clique em **Guardar e iniciar** a descoberta para iniciar o processo de descoberta.

Isto começa a ser descoberto. Leva cerca de 15 minutos para que os metadados de VMs descobertos apareçam no portal Azure.

## <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a descoberta, pode verificar se os VMs aparecem no portal.

1. Abra o painel de migração Azure.
2. No **Azure Migrate - Servers** > **Azure Migrar:** Página de Avaliação do Servidor, clique no ícone que apresenta a contagem para **servidores descobertos**.


## <a name="next-steps"></a>Passos seguintes

Experimente a [avaliação hyper-V](tutorial-assess-hyper-v.md) com a Avaliação do Servidor Migratório Azure.
