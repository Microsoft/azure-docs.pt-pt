---
title: Crie um aparelho de escala Azure Migrate para migração sem agente VMware
description: Saiba como configurar um aparelho de escala Azure Migrate para migrar Hiper-VMs.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 634eb2d22e3fa570ac9412d4fb8afd917b5c2eaa
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564012"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Migração sem agentes de máquinas virtuais VMware para Azure

Este artigo ajuda-o a entender como usar um aparelho de escala para migrar um grande número de máquinas virtuais VMware (VMs) para Azure usando o método de migração de servidores Azure Migrate Server para a migração de VMware VMs.

Utilizando o método de migração sem agente para máquinas virtuais VMware pode:

- Replique até 300 VMs de um único servidor vCenter simultaneamente utilizando um aparelho Azure Migrate.
- Replique até 500 VMs de um único servidor vCenter simultaneamente, implantando um segundo aparelho de escala para migração.

Neste artigo, aprenderá a:

- Adicione um aparelho de escala para migração sem agentes de máquinas virtuais VMware
- Migrar até 500 VMs simultaneamente utilizando o aparelho de encai escama.

##  <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tem de executar os seguintes passos:

- Criar o projeto Azure Migrate.
- Coloque o aparelho Azure Migrate (aparelho primário) e a descoberta completa de máquinas virtuais VMware geridas pelo seu servidor vCenter.
- Configure a replicação para uma ou mais máquinas virtuais que devem ser migradas.
> [!IMPORTANT]
> Terá de ter pelo menos uma máquina virtual replicante no projeto antes de poder adicionar um aparelho de escala para migração.

Para aprender a executar o acima, reveja o tutorial sobre [máquinas virtuais VMware migradoras para Azure com o método de migração sem agente](./tutorial-migrate-vmware.md).

## <a name="deploy-a-scale-out-appliance"></a>Implementar um aparelho de escala

Para adicionar um aparelho de escala, siga os passos abaixo mencionados:

1. Clique em **Descobrir**  >  **As máquinas estão virtualizadas?** 
1. Selecione **Sim, com VMware VSphere Hypervisor.**
1. Selecione a replicação sem agente no próximo passo.
1. Selecione **Scale-out um aparelho primário existente** no menu de selecionar o tipo de aparelho.
1. Selecione o aparelho primário (o aparelho utilizando o qual foi efetuada a descoberta) que pretende eliminar.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Descubra a página das máquinas para a escala no embarque":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Gerar a chave do projeto Azure Migrate

1. Na **tecla de projeto Generate Azure Migrate,** forneça um sufixo para o aparelho de escala. O sufixo pode conter apenas caracteres alfanuméricos e tem um limite de comprimento de 14 caracteres.
2. Clique **em Gerar chave** para iniciar a criação dos recursos Azure necessários. Por favor, não feche a página Discover durante a criação de recursos.
3. Copie a chave gerada. Mais tarde, necessitará da chave para completar o registo do aparelho de escala.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. Descarregue o instalador para o aparelho de encaamento

No **download do aparelho Azure Migrate,** clique em  **Baixar**. É necessário descarregar o script do instalador PowerShell para implantar o aparelho de escala num servidor existente que executa o Windows Server 2016 e com a configuração de hardware necessária (RAM de 32 GB, 8 vCPUs, cerca de 80 GB de armazenamento de disco e acesso à Internet, quer diretamente quer através de um proxy).
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Baixe o script para aparelho de escala":::

> [!TIP]
> Pode validar a parte de verificação do ficheiro zip descarregado utilizando estes passos:
>
> 1. Abrir o pedido de comando como administrador
> 2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Uso de exemplo para nuvem pública: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. Descarregue a versão mais recente do instalador de aparelhos de escala do portal se o valor do hash calculado não coincidir com esta cadeia: e9c9a1fe4f3ebae81008328e8f3a793d78ff835ecd871d1b17f36721ce3c74

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Executar o script do instalador Azure Migrate
O script do instalador faz o seguinte:

- Instala o agente de gateway e o gestor de configuração do aparelho para executar mais replicações simultâneas do servidor.
- Instale funções windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarregue e instale um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM) com detalhes de definição persistente para Azure Migrate.
- Cria os seguintes ficheiros no caminho:
    - **Config Ficheiros**: %Programdata%\Microsoft Azure\Config
    - **Ficheiros de registo**: %Programdata%\Microsoft Azure\Logs

Execute o guião da seguinte forma:

1. Extraia o ficheiro zip para uma pasta no servidor que irá hospedar o aparelho de escala.  Certifique-se de que não executa o script num servidor com um aparelho Azure Migrate existente.
2. Lançar PowerShell no servidor acima com privilégio administrativo (elevado).
3. Mude o diretório PowerShell para a pasta onde o conteúdo foi extraído do ficheiro zip descarregado.
4. Executar o script nomeado **AzureMigrateInstaller.ps1**  utilizando o seguinte comando:

    - Para a nuvem pública: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    O script lançará o gestor de configuração do aparelho quando completar a execução.

Se encontrar algum problema, pode aceder aos registos do script em: <br/> C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timetamp</em>.log para resolução de problemas.


### <a name="4-configure-the-appliance"></a>4. Configurar o aparelho

Antes de começar, certifique-se de que [estes pontos finais Azure](migrate-appliance.md#public-cloud-urls) estão acessíveis a partir do aparelho de encaimento.

- Abra um browser em qualquer máquina que possa ligar ao servidor do aparelho de escala e abra o URL do gestor de configuração do aparelho: **https:// nome do aparelho de escala ou endereço *IP*: 44368**.

   Em alternativa, pode abrir o gestor de configuração a partir do ambiente de trabalho do servidor do aparelho de escala utilizando o atalho para o gestor de configuração.
- Aceite os termos da **licença** e leia as informações de terceiros.
- No gestor de configuração > **Configurar pré-requisitos,** faça o seguinte:
   - **Conectividade**: O aparelho verifica se o servidor tem acesso à Internet. Se o servidor utilizar um representante:
     1. Clique em **Configurar o representante** para especificar o endereço de procuração (no formulário http://ProxyIPAddress ou na porta de http://ProxyFQDN) audição.
     2. Especifique as credenciais se o proxy precisar de autenticação.
     3. Apenas é suportado o proxy HTTP.
     4. Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
   - **Sincronização de tempo**: O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
   - **Instalação de atualizações**: O aparelho assegura a instalação das atualizações mais recentes. Depois de concluída a verificação, pode clicar nos **serviços do aparelho ver** o estado e as versões dos serviços em execução no servidor do aparelho.
   - **Instalação VDDK**: O aparelho verifica se o Kit de Desenvolvimento de Discos Virtuais VMware vSphere (VDDK) está instalado. Se não estiver instalado, descarregue O VDDK 6.7 da VMware e extraia o conteúdo zip descarregado para o local especificado no aparelho, conforme indicado nas **instruções de instalação** no ecrã do Gestor de Configuração do Aparelho.


### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Cole a chave do **projeto Azure Migrate** copiada do portal. Se não tiver a chave, vá à Avaliação do Servidor> Descubra> Gerir os **aparelhos existentes**, selecione o nome do aparelho primário, encontre o aparelho de escala associado e copie a chave correspondente.
1. Necessitará de um código de dispositivo para autenticar com o Azure. Clicar no **Login** abrirá um código modal com o código do dispositivo, como mostrado abaixo.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modal mostrando o código do dispositivo":::

1. Clique no **código copy & Iniciar sessão** para copiar o código do dispositivo e abrir um pedido de Login Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
1. No novo separador, cole o código do dispositivo e inscreva-se utilizando o seu nome de utilizador Estaure e palavra-passe.
   
   O s-in com um PIN não é suportado.
3. Caso feche o separador de login acidentalmente sem iniciar sessão, é necessário atualizar o separador de navegador do gestor de configuração do aparelho para ativar novamente o botão Iniciar sessão.
1. Depois de ter iniciado sessão com sucesso, volte ao separador anterior com o gestor de configuração do aparelho.
1. Se a conta de utilizador Azure utilizada para a exploração madeireira tiver as permissões certas sobre os recursos Azure criados durante a geração chave, o registo do aparelho será iniciado.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Painel 2 no gestor de configuração do aparelho":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Importar configuração do aparelho a partir de aparelho primário

Para completar o registo do aparelho de escala, clique em **importação** para obter os ficheiros de configuração necessários do aparelho primário.

1. A **importação** de cliques abre uma janela pop-up com instruções sobre como importar os ficheiros de configuração necessários do aparelho primário.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Configuração de importação modal":::
1. Faça login (ambiente de trabalho remoto) para o aparelho primário e execute os seguintes comandos PowerShell:

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. Copie o ficheiro zip criado executando os comandos acima para o aparelho de escala. O ficheiro zip contém ficheiros de configuração necessários para registar o aparelho de escala.
1. Na janela pop-up aberta no passo anterior, selecione a localização do ficheiro zip de configuração copiada e clique em **Guardar**.

Uma vez importados com sucesso os ficheiros, o registo do aparelho de escala completa-se e mostrar-lhe-á o tempo de marcação da última importação bem sucedida. Também pode ver os detalhes do registo clicando em **Ver detalhes.**
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="A screenshot mostra o registo de aparelhos em escala com o projeto Azure Migrate.":::

Neste ponto, deverá revalidar que o aparelho de escala é capaz de se ligar ao servidor vCenter. Clique **em revalidar** para validar a conectividade do servidor vCenter a partir do aparelho de escala.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="A screenshot mostra credenciais de visualização e fontes de descoberta a validar.":::

> [!IMPORTANT]
> Se editar as credenciais do vCenter Server no aparelho primário, certifique-se de que importa novamente os ficheiros de configuração para o aparelho de escala para obter a configuração mais recente e continuar quaisquer replicações em curso.<br/> Se não precisar mais do aparelho de encadação, certifique-se de que desativa o aparelho de encai escama. [**Saiba mais**](./common-questions-appliance.md) sobre como desativar o aparelho de encai escama quando não for necessário.

## <a name="replicate"></a>Replicar

1. Depois de registado o aparelho de escala, no Azure Migrate: Telha de migração do servidor, clique em **Replicar**.

2.  Siga os passos no ecrã para começar a replicar mais máquinas virtuais. 

Com o aparelho de escotamia no lugar, pode agora replicar 500 VMs simultaneamente. Também pode migrar VMs em lotes de 200 através do portal Azure.

A ferramenta de migração do servidor Azure Migrate cuidará da distribuição das máquinas virtuais entre o aparelho primário e o aparelho de escala para replicação. Uma vez feita a replicação, pode migrar as máquinas virtuais.

> [!TIP]
> Recomendamos a migração de máquinas virtuais em lotes de 200 para um desempenho ótimo se quiser migrar um grande número de máquinas virtuais.
  
## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu:
- Como configurar um aparelho de escala
- Como replicar VMs usando um aparelho de escala


[Saiba mais](./tutorial-migrate-vmware.md) sobre a migração de servidores para Azure usando a ferramenta Azure Migrate: Server Migration.