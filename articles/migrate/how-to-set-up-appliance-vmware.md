---
title: Crie um aparelho Azure Migrate para VMware
description: Saiba como configurar um aparelho Azure Migrate para avaliar e migrar VMware VMs.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 272481640db739a458a19a2c9383ff45b54974b5
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86112821"
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
- Descarregue um ficheiro de modelo OVA e importe-o para o vCenter Server.
- Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Azure Migrate.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.

## <a name="download-the-ova-template"></a>Descarregue o modelo OVA

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, click **.click**.
2. In **Discover machines**  >  **Are your machines virtualized?** **Yes, with VMWare vSphere hypervisor**
3. Clique em **Transferir** para transferir o ficheiro de modelo .OVA.

  ![Seleções para descarregar um ficheiro OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro OVA está seguro, antes de o implementar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando, para gerar o haxixe para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para a versão mais recente do aparelho, o haxixe gerado deve coincidir com estas [definições](./tutorial-assess-vmware.md#verify-security).



## <a name="create-the-appliance-vm"></a>Criar o aparelho VM

Importe o ficheiro descarregado e crie um VM.

1. Na consola vSphere Client, clique em **File**  >  **Ficheiros Implementar OVF Modelo**.
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


## <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez. Se utilizar o aparelho utilizando um script em vez de um modelo OVA, os dois primeiros passos do procedimento não são aplicáveis.

1. Na consola do vSphere Client, clique com o botão direito do rato na VM > **Abrir Consola**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer máquina que possa ligar ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho clicando no atalho da aplicação.
4. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um representante:
        - Clique **nas definições de Procuração**e especifique o endereço de procuração e a porta de audição, no formulário http://ProxyIPAddress ou http://ProxyFQDN .
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização temporal:** O tempo é verificado. O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
    - **Instalar atualizações**: A Azure Migrate verifica se as atualizações mais recentes do aparelho estão instaladas.
    - **Instalar VDDK**: Azure Migrate verifica se o Kit de Desenvolvimento de Discos Virtuais VMWare vSphere (VDDK) está instalado.
        - Azure Migrates usa o VDDK para replicar máquinas durante a migração para Azure.
        - Baixe o VDDK 6.7 da VMware e extraia o conteúdo zip descarregado para a localização especificada no aparelho.

## <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Clique **em iniciar sessão**. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
2. No novo separador, inscreva-se utilizando as suas credenciais Azure.
    - Inscreva-se com o seu nome de utilizador e senha.
    - Iniciar sedução com um PIN não é suportado.
3. Depois de iniciar sessão com sucesso, volte à aplicação web.
2. Selecione a subscrição em que o projeto Azure Migrate foi criado. Em seguida, selecione o projeto.
3. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
4. Clique em **Registar**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Inicie a descoberta contínua fornecendo o servidor vCenter e a credencial VM

O aparelho precisa de se ligar ao servidor vCenter para descobrir a configuração e os dados de desempenho dos VMs.

### <a name="specify-vcenter-server-details"></a>Especificar detalhes do vCenter Server
1. Nos **detalhes do Servidor Do VCenter,** especifique o nome (FQDN) ou endereço IP do servidor vCenter. Pode deixar a porta predefinida ou especificar uma porta personalizada na qual o seu vCenter Server ouve.
2. No **nome de utilizador** e na **palavra-passe**, especifique as credenciais de conta apenas de leitura que o aparelho utilizará para descobrir VMs no servidor vCenter. Pode fazer o âmbito da descoberta limitando o acesso à conta vCenter. [Saiba mais](set-discovery-scope.md).
3. Clique **em Validar** a ligação para se certificar de que o aparelho pode ligar-se ao servidor vCenter.

### <a name="specify-vm-credentials"></a>Especificar credenciais VM
Para a descoberta de aplicações, funções e funcionalidades e visualizar dependências dos VMs, pode fornecer uma credencial VM que tenha acesso aos VMware VMs. Pode adicionar uma credencial para VMs windows e uma credencial para Os VMs Do Linux. [Saiba mais](./migrate-support-matrix-vmware.md) sobre os privilégios de acesso necessários.

> [!NOTE]
> Esta entrada é opcional e é necessária para permitir a descoberta de aplicações e visualização de dependência sem agentes.

1. In **Discover applications and dependências em VMs,** clique em **Adicionar credenciais**.
2. Selecione o **Sistema Operativo.**
3. Forneça um nome amigável para a credencial.
4. No **nome de utilizador** e **palavra-passe,** especifique uma conta que tenha pelo menos acesso ao hóspede nos VMs.
5. Clique em **Adicionar**.

Uma vez especificado as credenciais vCenter Server e VM (opcional), clique em **Guardar e iniciar** a descoberta para iniciar a descoberta do ambiente no local.

Leva cerca de 15 minutos para que os metadados de VMs descobertos apareçam no portal. A descoberta de aplicações, funções e funcionalidades instaladas demora algum tempo, a duração depende do número de VMs a serem descobertos. Para 500 VMs, leva aproximadamente 1 hora para o inventário da aplicação aparecer no portal Azure Migrate.

## <a name="next-steps"></a>Próximos passos

Reveja os tutoriais para [avaliação de VMware](tutorial-assess-vmware.md) e [migração sem agentes.](tutorial-migrate-vmware.md)
