---
title: Crie um aparelho Azure Migrate para VMware
description: Saiba como configurar um aparelho Azure Migrate para avaliar e migrar VMware VMs.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 749dd17c6f9b09db1841459da951b44470d8bb92
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704712"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurar um aparelho para VMware VMs

Siga este artigo para configurar o aparelho Azure Migrate para avaliação com a ferramenta [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) e para migração sem agente utilizando a ferramenta [migração Azure Migrate:Server Migration.](migrate-services-overview.md#azure-migrate-server-migration-tool)

O [aparelho Azure Migrate](migrate-appliance.md) é um aparelho leve utilizado pela Azure Migrate:Server Assessment and Server Migration para descobrir VMware VMware no local, enviar metadados/dados de desempenho VM para Azure, e para replicação de VMware VMs durante a migração sem agentes.

Pode utilizar o aparelho utilizando um par de métodos:

- Configurar um VMware VM usando um modelo OVA descarregado. Este é o método descrito neste artigo.
- Configurar num VMware VM ou numa máquina física com um script instalador PowerShell. [Este método](deploy-appliance-script.md) deve ser usado se não puder configurar um VM usando um modelo OVA, ou se estiver no governo de Azure.

Depois de criar o aparelho, verifique se pode ligar-se ao Azure Migrate:Server Assessment, configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.


## <a name="appliance-deployment-ova"></a>Implantação do aparelho (OVA)

Para configurar o aparelho utilizando um modelo OVA:
- Forneça um nome de aparelho e gere uma chave de projeto Azure Migrate no portal
- Descarregue um ficheiro de modelo OVA e importe-o para o vCenter Server.
- Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Azure Migrate.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate utilizando a chave do projeto Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Gere a chave do projeto Azure Migrate

1. Em **Objetivos de Migração** > **Servidores** > **Azure Migrate: Avaliação do Servidor**, selecione **Detetar**.
2. In **Discover machines**  >  **Are your machines virtualized?** 
3. Na **tecla de projeto 1:Generate Azure Migrate,** forneça um nome para o aparelho Azure Migrate que irá configurar para a descoberta de VMware VMs.O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique na **chave Gerar** para iniciar a criação dos recursos Azure necessários. Por favor, não feche a página das máquinas Discover durante a criação de recursos.
1. Após a criação bem sucedida dos recursos Azure, é gerada uma **chave de projeto Azure Migrate.**
1. Copie a chave pois necessitará para completar o registo do aparelho durante a sua configuração.

### <a name="download-the-ova-template"></a>Descarregue o modelo OVA
Em **2: Descarregue o aparelho Azure Migrate,** selecione o . Ficheiro OVA e clique no **Download**. 


   ![Seleções para máquinas Discover](./media/tutorial-assess-vmware/servers-discover.png)


   ![Seleções para Gerar Tecla](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro OVA está seguro, antes de o implementar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando, para gerar o haxixe para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```
3. Para a versão mais recente do aparelho, o haxixe gerado deve coincidir com estas [definições](./tutorial-discover-vmware.md#verify-security).



## <a name="create-the-appliance-vm"></a>Criar o aparelho VM

Importe o ficheiro descarregado e crie um VM.

1. Na consola vSphere Client, clique em   >  **Ficheiros Implementar OVF Modelo**.
![Comando do menu para implantar um modelo OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. No Assistente de Modelo de OVF de implementação > **Fonte**, especifique a localização do ficheiro OVA.
3. No **Nome** e **Localização**, especifique um nome amigável para o VM. Selecione o objeto de inventário no qual o VM será hospedado.
5. No **Host/Cluster**, especifique o hospedeiro ou o cluster em que o VM irá funcionar.
6. No **Armazenamento**, especifique o destino de armazenamento para o VM.
7. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
8. No **Mapeamento de Rede,** especifique a rede à qual o VM se ligará. A rede precisa de conectividade com a Internet, para enviar metadados para a Avaliação do Servidor Migratório Azure.
9. Reveja e confirme as definições e, em seguida, clique em **Concluir**.


## <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho VM pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)


### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [script PowerShell](deploy-appliance-script.md) em vez do OVA descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. Na consola vSphere Client, clique com o botão direito no VM e, em seguida, selecione **Open Console**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer máquina que possa ligar ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço *IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho selecionando o atalho da aplicação.
1. Aceite os termos da **licença** e leia as informações de terceiros.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
   - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um representante:
     - Clique em **Configurar o representante** para especificar o endereço de procuração (no formulário http://ProxyIPAddress ou na porta de http://ProxyFQDN) audição.
     - Especifique as credenciais se o proxy precisar de autenticação.
     - Apenas é suportado o proxy HTTP.
     - Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
   - **Sincronização de tempo**: O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
   - **Instalação de atualizações**: O aparelho assegura a instalação das atualizações mais recentes. Depois de concluída a verificação, pode clicar nos **serviços do aparelho para** ver o estado e as versões dos componentes em funcionamento no aparelho.
   - **Instalação VDDK**: O aparelho verifica se o Kit de Desenvolvimento de Discos Virtuais VMware vSphere (VDDK) está instalado. Se não estiver instalado, faça o download do VDDK 6.7 da VMware e extraia o conteúdo zip descarregado para o local especificado no aparelho, conforme indicado nas **instruções de instalação**.

     A migração do servidor Azure Migrate usa o VDDK para replicar máquinas durante a migração para Azure. 
1. Se desejar, pode **repetir os pré-requisitos** a qualquer momento durante a configuração do aparelho para verificar se o aparelho cumpre todos os requisitos.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Cole a chave do **projeto Azure Migrate** copiada do portal. Se não tiver a chave, vá à Avaliação do Servidor> Descubra> Gerir os **aparelhos existentes**, selecione o nome do aparelho que forneceu no momento da geração de chaves e copie a chave correspondente.
1. Necessitará de um código de dispositivo para autenticar com o Azure. Clicar no **Login** abrirá um código modal com o código do dispositivo, como mostrado abaixo.

    ![Modal mostrando o código do dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Clique no **código copy & Iniciar sessão** para copiar o código do dispositivo e abrir um pedido de Login Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
1. No novo separador, cole o código do dispositivo e inscreva-se utilizando o seu nome de utilizador Estaure e palavra-passe.
   
   O s-in com um PIN não é suportado.
3. Caso feche o separador de login acidentalmente sem iniciar sessão, é necessário atualizar o separador de navegador do gestor de configuração do aparelho para ativar novamente o botão Iniciar sessão.
1. Depois de iniciar sessão com sucesso, volte ao separador anterior com o gestor de configuração do aparelho.
4. Se a conta de utilizador Azure utilizada para a exploração madeireira tiver as [permissões certas](./tutorial-discover-vmware.md#prepare-an-azure-user-account) sobre os recursos Azure criados durante a geração chave, o registo do aparelho será iniciado.
1. Depois de o aparelho estar registado com sucesso, pode ver os dados do registo clicando nos **detalhes do Ver.**


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O aparelho precisa de se ligar ao servidor vCenter para descobrir a configuração e os dados de desempenho dos VMs.

1. No **passo 1: Forneça credenciais do servidor vCenter**, clique em **Adicionar credenciais** para especificar um nome amigável para credenciais, adicionar **nome de utilizador** e **palavra-passe** para a conta vCenter Server que o aparelho utilizará para descobrir VMs na instância do servidor vCenter.
    - Deveria ter criado uma conta com as permissões necessárias no [tutorial anterior.](./tutorial-discover-vmware.md#create-an-account-to-access-vcenter)
    - Se pretender estender a descoberta a objetos VMware específicos (centros de dados vCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou VMs individuais.), reveja as instruções [deste artigo](set-discovery-scope.md) para restringir a conta utilizada pela Azure Migrate.
1. No **passo 2: Forneça detalhes do servidor vCenter**, clique na **fonte de descoberta Adicionar** para selecionar o nome amigável para credenciais a partir do drop-down, especificar o endereço **IP/FQDN** da instância vCenter Server. Pode deixar a **Porta** para predefinição (443) ou especificar uma porta personalizada na qual o vCenter Server ouve e clica em **Guardar**.
1. Ao clicar em Guardar, o aparelho tentará validar a ligação ao servidor vCenter com as credenciais fornecidas e mostrar o **estado de Validação** na tabela contra o endereço IP/FQDN do servidor vCenter.
1. Pode **revalidar** a conectividade ao vCenter Server a qualquer momento antes de iniciar a descoberta.
1. No **passo 3: Forneça credenciais VM para descobrir aplicações instaladas e realizar mapeamento de dependência sem agente,** clique **em Adicionar credenciais,** e especifique o sistema operativo para o qual as credenciais são fornecidas, nome amigável para credenciais e o **nome** de utilizador e **senha**. Em seguida, clique em **Guardar**.

    - Você opcionalmente adiciona credenciais aqui se você criou uma conta para usar para o recurso de descoberta de [aplicações](how-to-discover-applications.md), ou a [funcionalidade de análise de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se não quiser utilizar estas funcionalidades, pode clicar no slider para saltar o passo. Pode inverter a intenção a qualquer momento depois.
    - Reveja as credenciais necessárias para [a descoberta de aplicações,](migrate-support-matrix-vmware.md#application-discovery-requirements)ou para [análise de dependência sem agentes.](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)

5. Clique em **Iniciar a descoberta,** para iniciar a descoberta de VM. Após a descoberta ter sido iniciada com sucesso, pode verificar o estado de descoberta com o endereço IP/FQDN do servidor vCenter na tabela.

A descoberta funciona da seguinte forma:
- Leva cerca de 15 minutos para que os metadados VM descobertos apareçam no portal.
- A descoberta de aplicações, funções e funcionalidades instaladas leva algum tempo. A duração depende do número de VMs descobertos. Para 500 VMs, leva aproximadamente uma hora para o inventário da aplicação aparecer no portal Azure Migrate.

## <a name="next-steps"></a>Passos seguintes

Reveja os tutoriais para [avaliação de VMware](./tutorial-assess-vmware-azure-vm.md) e [migração sem agentes.](tutorial-migrate-vmware.md)