---
title: Instale um aparelho Azure Migrate para Hyper-V
description: Aprenda a montar um aparelho Azure Migrate para avaliar e migrar VMs Hiper-V.
ms.topic: article
ms.date: 11/19/2019
ms.openlocfilehash: 8199525a118ffca2cfc03734283eb26facba8483
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598345"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurar um aparelho para VMs hiper-V

Este artigo descreve como configurar o aparelho Azure Migrate se estiver a avaliar os VMs Hiper-V com a ferramenta de avaliação do servidor de migração Azure, ou a migrar VMware VMs para Azure utilizando a ferramenta de migração do servidor migratório Azure Migrate.

O aparelho Hyper-V VM é um aparelho leve utilizado pela Azure Migrate Server Assessment/Migration para fazer o seguinte:

- Descubra no local Hiper-V VMs.
- Envie metadados e dados de desempenho para VMs descobertos para A Avaliação/Migração do Servidor Migratório Azure.

[Saiba mais](migrate-appliance.md) sobre o aparelho Azure Migrate.


## <a name="appliance-deployment-steps"></a>Passos de implantação de aparelhos

Para configurar o aparelho:
- Baixe um VHD Hiper-V comprimido a partir do portal Azure.
- Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Migratório Azure.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.

## <a name="download-the-vhd"></a>Descarregue o VHD

Descarregue o modelo VHD com fecho para o aparelho.

1. Em **Objetivos de Migração** > **servidores** > **Azure Migrate: Avaliação do servidor,** clique **em Descobrir**.
2. Em **Discover machines** > **As suas máquinas estão virtualizadas?**
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
2. Open Hyper-V Manager. Em **Ações,** clique **em Importar Máquina Virtual**.

    ![Implementar VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. No Importador Virtual Machine Wizard > Antes de **começar,** clique **em Seguinte**.
3. Na **pasta localizar,** especifique a pasta que contém o VHD extraído. Clique depois em **Seguinte**.
1. Na **selecione Máquina Virtual,** clique em **Seguinte**.
2. Em **Escolher o Tipo de Importação,** clique em copiar a máquina virtual **(criar um novo ID único)** . Clique depois em **Seguinte**.
3. No **Destino Escolha,** deixe a definição predefinida. Clique em **Seguinte**.
4. Nas **pastas de armazenamento,** deixe a definição predefinida. Clique em **Seguinte**.
5. Na **Rede Escolha,** especifique o interruptor virtual que o VM utilizará. O interruptor precisa de conectividade de internet para enviar dados para o Azure.
6. Em **resumo,** reveja as definições. Em seguida, clique em **Terminar**.
7. Em Hyper-V Manager > **Virtual Machines,** inicie o VM.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o VM do aparelho pode ligar-se a [URLs Azure](migrate-appliance.md#url-access).

## <a name="configure-the-appliance"></a>Configure o aparelho

Instale o aparelho pela primeira vez.

1. Em Hyper-V Manager > **Virtual Machines,** clique à direita no VM > **Connect**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um navegador em qualquer máquina que possa ligar-se ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho clicando no atalho da aplicação.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um proxy:
        - Clique em **definições proxy**, e especifique o endereço proxy e a porta de escuta, sob a forma http://ProxyIPAddress ou http://ProxyFQDN.
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
    - Na **política informática local** > **configuração do computador,** clique em **modelos administrativos** > **sistema** > delegação de **credenciais.**
    - Clique duplo **Para permitir a delegação**de novas credenciais e selecionar **Ativado**.
    - Em **Opções,** clique em **Mostrar**, e adicione cada anfitrião Hyper-V que deseja descobrir na lista, com **wsman/** como prefixo.
    - Na **Delegação de Credenciais,** clique duplo **Permita delegar novas credenciais com autenticação de servidor apenas NTLM**. Mais uma vez, adicione cada anfitrião Hyper-V que deseja descobrir na lista, com **wsman/** como prefixo.

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Ligue-se do aparelho aos anfitriões ou aglomerados hyper-V e inicie a descoberta vm.

1. No **nome do utilizador** e na **palavra-passe,** especifique as credenciais de conta que o aparelho utilizará para descobrir VMs. Especifique um nome amigável para as credenciais e clique em **Guardar detalhes**.
2. Clique em **Adicionar anfitrião**e especificar detalhes do hospedeiro/cluster hyper-V.
3. Clique em **Validar**. Após validação, é mostrado o número de VMs que podem ser descobertos em cada hospedeiro/cluster.
    - Se a validação falhar para um hospedeiro, reveja o erro pairando sobre o ícone na coluna **'Estado'.** Corrija os problemas e valide novamente.
    - Para remover hospedeiros ou clusters, selecione > **Delete**.
    - Não se pode remover um hospedeiro específico de um aglomerado. Só se pode remover todo o aglomerado.
    - Pode adicionar um cluster, mesmo que existam problemas com anfitriões específicos no cluster.
4. Após validação, clique em **Guardar e iniciar** a descoberta para iniciar o processo de descoberta.

Isto começa a ser descoberto. Leva cerca de 15 minutos para que os metadados de VMs descobertos apareçam no portal Azure.

## <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a descoberta, pode verificar se os VMs aparecem no portal.

1. Abra o painel de migração Azure.
2. Em **Azure Migrate - Servidores** > **Azure Migrar:** Página de Avaliação do Servidor, clique no ícone que apresenta a contagem para **servidores descobertos**.


## <a name="next-steps"></a>Passos seguintes

Experimente a [avaliação hyper-V](tutorial-assess-hyper-v.md) com a Avaliação do Servidor Migratório Azure.
