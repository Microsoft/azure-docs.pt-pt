---
title: Instale um aparelho Azure Migrate para VMware
description: Aprenda a configurar um aparelho Azure Migrate para avaliar e migrar VMware VMware VMs.
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: e331d45d3e87f8007642675a0349839e7494958c
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598158"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurar um aparelho para VMware VMs

Este artigo descreve como configurar o aparelho Azure Migrate se estiver a avaliar vMware VMs com a ferramenta de avaliação do servidor de migração Azure, ou vMs migratórios para Azure com migração sem agente usando a ferramenta de migração do servidor migratório Azure Migrate.

O aparelho VMware VM é um aparelho leve utilizado pela Azure Migrate Server Assessment/Migration para fazer o seguinte:

- detete VMs VMware no local.
- Envie metadados e dados de desempenho para VMs descobertos para A Avaliação/Migração do Servidor Migratório Azure.

[Saiba mais](migrate-appliance.md) sobre o aparelho Azure Migrate.


## <a name="appliance-deployment-steps"></a>Passos de implantação de aparelhos

Para configurar o aparelho:
- Descarregue um ficheiro de modelo OVA e importe-o para vCenter Server.
- Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Migratório Azure.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.

## <a name="download-the-ova-template"></a>Descarregue o modelo OVA

1. Em **Objetivos de Migração** > **servidores** > **Azure Migrate: Avaliação do servidor,** clique **em Descobrir**.
2. Nos **Descobrir computadores** > **Os computadores estão virtualizados?** , clique **Sim, com o hipervisor VMware vSphere**.
3. Clique em **Transferir** para transferir o ficheiro de modelo .OVA.

  ![Seleções para descarregar um ficheiro OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verificar a segurança

Verifique se o ficheiro OVA está seguro, antes de o implementar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando, para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para a versão mais recente do aparelho, o hash gerado deve coincidir com estas [definições](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security).



## <a name="create-the-appliance-vm"></a>Crie o vm do aparelho

Importe o ficheiro descarregado e crie um VM.

1. Na consola do vSphere Client, clique em **Ficheiro** > **Implementar Modelo OVF**.
comando de menu ![para a implementação de um modelo OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. No modelo de implantação do MODELO OVF Assistente > **Fonte,** especifique a localização do ficheiro OVA.
3. Em **Nome** e **Localização,** especifique um nome amigável para o VM. Selecione o objeto de inventário no qual o VM será hospedado.
5. No **hospedeiro/cluster,** especifique o hospedeiro ou o cluster em que o VM irá funcionar.
6. No **Armazenamento,** especifique o destino de armazenamento para o VM.
7. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
8. No **Mapeamento da Rede,** especifique a rede à qual o VM irá ligar. A rede precisa de conectividade de internet para enviar metadados para a Avaliação do Servidor Migratório Azure.
9. Reveja e confirme as definições e, em seguida, clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o VM do aparelho pode ligar-se a [URLs Azure](migrate-appliance.md#url-access).


## <a name="configure-the-appliance"></a>Configure o aparelho

Instale o aparelho pela primeira vez.

1. Na consola do vSphere Client, clique com o botão direito do rato na VM > **Abrir Consola**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um navegador em qualquer máquina que possa ligar-se ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho clicando no atalho da aplicação.
4. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um proxy:
        - Clique em **definições proxy**, e especifique o endereço proxy e a porta de escuta, sob a forma http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de tempo**: O tempo é verificado. O tempo no aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
    - **Instalar atualizações**: A Azure Migrate verifica se as últimas atualizações do aparelho estão instaladas.
    - **Instale VDDK**: Azure Migrate verifica se o VMWare vSphere Virtual Disk Development Kit (VDDK) está instalado.
        - A Azure Migrates usa o VDDK para replicar máquinas durante a migração para O Azure.
        - Baixe o VDDK 6.7 da VMware e extraia o conteúdo de zip descarregado para o local especificado no aparelho.

## <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Clique **em iniciar sessão**. Se não aparecer, certifique-se de que desativou o bloqueador pop-up no navegador.
2. No novo separador, inscreva-se utilizando as suas credenciais Azure.
    - Inscreva-se com o seu nome de utilizador e senha.
    - Iniciar sessão com um PIN não é suportado.
3. Depois de iniciar sessão com sucesso, volte para a aplicação web.
2. Selecione a subscrição em que foi criado o projeto Azure Migrate. Em seguida, selecione o projeto.
3. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
4. Clique no **Registo**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Inicie a descoberta contínua fornecendo vCenter Server e credencial VM

O aparelho precisa de se ligar ao VCenter Server para descobrir os dados de configuração e desempenho dos VMs.

### <a name="specify-vcenter-server-details"></a>Especificar detalhes do vCenter Server
1. Em especificar os detalhes do **servidor vCenter,** especifique o nome (FQDN) ou o endereço IP do servidor vCenter. Pode deixar a porta predefinida ou especificar uma porta personalizada na qual o seu vCenter Server ouve.
2. No **nome do utilizador** e na **palavra-passe,** especifique as credenciais de conta apenas de leitura que o aparelho utilizará para descobrir VMs no servidor vCenter. Pode analisar a descoberta limitando o acesso à conta vCenter em conformidade; saiba mais sobre a descoberta de scoping [aqui.](tutorial-assess-vmware.md#set-the-scope-of-discovery)
3. Clique em **relação validar** a ligação para se certificar de que o aparelho pode ligar-se ao VCenter Server.

### <a name="specify-vm-credentials"></a>Especificar credenciais VM
Para a descoberta de aplicações, funções e funcionalidades e visualização de dependências dos VMs, pode fornecer uma credencial VM que tem acesso aos VMware VMs. Pode adicionar uma credencial para VMs windows e uma credencial para VMs Linux. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) sobre os privilégios de acesso necessários.

> [!NOTE]
> Esta entrada é opcional e é necessária para permitir a descoberta da aplicação e a visualização da dependência sem agente.

1. Em **Discover aplicações e dependências em VMs,** clique em **Adicionar credenciais.**
2. Selecione o **Sistema Operativo**.
3. Forneça um nome amigável para a credencial.
4. No **Username** e **Password,** especifique uma conta que tenha pelo menos acesso ao hóspede nos VMs.
5. Clique em **Adicionar**.

Depois de especificar as credenciais vCenter Server e VM (opcional), clique em **Guardar e comece** a descoberta para começar a descoberta do ambiente no local.

Leva cerca de 15 minutos para que os metadados de VMs descobertos apareçam no portal. A descoberta de aplicações, funções e funcionalidades instaladas leva algum tempo, a duração depende do número de VMs descobertos. Para 500 VMs, leva aproximadamente 1 hora para que o inventário de aplicações apareça no portal Azure Migrate.

## <a name="next-steps"></a>Passos seguintes

Reveja os tutoriais para [avaliação vmware](tutorial-assess-vmware.md) e [migração sem agente.](tutorial-migrate-vmware.md)
