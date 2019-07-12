---
title: Configurar uma aplicação para o Azure Migrate avaliação/migração do servidor para VMs de Hyper-V | Documentos da Microsoft
description: Descreve como configurar um dispositivo para a deteção, avaliação e migração sem agente de VMs de Hyper-V através do Azure Migrate avaliação/migração do servidor.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811768"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurar uma aplicação para VMs de Hyper-V

Este artigo descreve como configurar a aplicação do Azure Migrate, se estiver a avaliar as VMs de Hyper-V com a ferramenta de avaliação do servidor de migrar do Azure ou, migrar VMs de VMware para o Azure com a ferramenta de migração do servidor de migrar do Azure.

A aplicação de VM de Hyper-V é uma simples aplicação utilizada pelo Azure Migrate avaliação/migração do servidor para fazer o seguinte:

- Detete VMs de Hyper-V no local.
- Envie dados de metadados e desempenho para as VMs detetadas para Azure Migrate avaliação/migração do servidor.

[Saiba mais](migrate-appliance.md) sobre a aplicação do Azure Migrate.


## <a name="appliance-deployment-steps"></a>Passos de implementação da aplicação

Para configurar a aplicação da:
- Transferir um VHD de Hyper-V comprimido do portal do Azure.
- Criar a aplicação e verifique se pode ligar a avaliação do servidor de migrar do Azure. 
- Configurar a aplicação pela primeira vez e registrá-la com o projeto do Azure Migrate.

## <a name="download-the-vhd"></a>Baixe o VHD

Transfira o modelo VHD zipado para a aplicação.

1. Na **objetivos de migração** > **servidores** > **do Azure Migrate: Avaliação de servidor**, clique em **detetar**.
2. Na **detetar máquinas** > **são as suas máquinas virtualizadas?** , clique em **Sim, com o Hyper-V**.
3. Clique em **transferir** para transferir o ficheiro VHD.

    ![Transferir a VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Certifique-se de segurança

Verifique se o arquivo zipado é seguro, antes de o implementar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Execute o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Para obter a versão da aplicação 1.19.05.10, o hash gerado deve corresponder a estas definições.

  **Algoritmo** | **Valor de hash**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>Criar a aplicação da VM

Importar o ficheiro transferido e criar a VM.

1. Extraia o ficheiro VHD zipado para uma pasta no anfitrião Hyper-V que irá alojar a aplicação da VM. Três pastas são extraídas.
2. Abra o Gestor de Hyper-V. Na **ações**, clique em **importar Máquina Virtual**.

    ![Implementar o VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. No Assistente para importar Máquina Virtual > **antes de começar**, clique em **próxima**.
3. Na **localizar pasta**, especifique a pasta que contém o VHD extraído. Clique depois em **Seguinte**.
1. Na **selecionar Máquina Virtual**, clique em **próxima**.
2. Na **escolha o tipo de importação**, clique em **copiar a máquina virtual (criar um novo ID exclusivo)** . Clique depois em **Seguinte**.
3. Na **escolha destino**, deixe a predefinição. Clique em **Seguinte**.
4. Na **pastas de armazenamento**, deixe a predefinição. Clique em **Seguinte**.
5. Na **escolher rede**, especificar o comutador virtual que irá utilizar a VM. O comutador necessita de conectividade de internet para enviar dados para o Azure.
6. Na **resumo**, reveja as definições. Em seguida, clique em **concluir**.
7. No Gestor de Hyper-V > **máquinas virtuais**, inicie a VM.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso da aplicação para o Azure

Certifique-se de que a aplicação da VM pode ligar à [URLs do Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Configurar a aplicação

Configure a aplicação pela primeira vez.

1. No Gestor de Hyper-V > **máquinas virtuais**, clique com o botão direito a VM > **Connect**.
2. Fornece o idioma, fuso horário e palavra-passe para a aplicação.
3. Abra um browser em qualquer máquina que pode ligar à VM e abrir o URL da aplicação web da aplicação: **https://*nome da aplicação ou o endereço IP*: 44368**.

   Em alternativa, pode abrir a aplicação no ambiente de trabalho da aplicação ao clicar no atalho de aplicação.
1. Na aplicação web > **configurar pré-requisitos**, efetue o seguinte procedimento:
    - **Licença**: Aceite os termos de licenciamento e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se a VM tem acesso à internet. Se a VM utiliza um proxy:
        - Clique em **definições de Proxy**e especifique o endereço de proxy e porta de escuta, o formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de hora**: Tempo é verificado. A hora da aplicação deve ser sincronizada com o tempo de internet para deteção de VMS para funcionarem corretamente.
    - **Instalar atualizações**: Avaliação de servidor migrar do Azure verifica se a aplicação tem as atualizações mais recentes instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registre-se a aplicação com o Azure Migrate

1. Clique em **iniciar sessão**. Se não aparecer, certifique-se de que ter desabilitado o Bloqueador de pop-up no browser.
2. Na nova guia, inicie sessão com as credenciais do Azure. 
    - Inicie sessão com o nome de utilizador e palavra-passe.
    - Inicie sessão com um PIN não é suportada.
3. Depois de iniciar sessão com êxito na, volte para a aplicação web.
4. Selecione a subscrição na qual foi criado o projeto do Azure Migrate. Em seguida, selecione o projeto.
5. Especifique um nome para a aplicação. O nome deve ser alfanumérica com 14 caracteres ou menos.
6. Clique em **registar**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegar credenciais para VHDs de SMB

Se estiver executando VHDs em PMEs, tem de ativar a delegação de credenciais a partir da aplicação para os anfitriões de Hyper-V. Para fazer isso partir da aplicação:

1. Na aplicação da VM, execute este comando. HyperVHost1/HyperVHost2 são nomes de anfitrião de exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Em alternativa, fazê-lo no Editor de políticas de grupo de Local na aplicação da:
    - Na **política de computador Local** > **configuração do computador**, clique em **modelos administrativos** > **sistema**  >  **Delegação de credenciais**.
    - Faça duplo clique em **permitir a delegação das credenciais de raiz**e selecione **ativado**.
    - Na **opções**, clique em **mostrar**, e adicione cada anfitrião de Hyper-V que pretende detetar à lista, com **wsman /** como um prefixo.
    - Na **delegação de credenciais**, faça duplo clique em **permitir delegação das credenciais de raiz com a autenticação de servidor somente de NTLM**. Adicionar mais uma vez, cada anfitrião de Hyper-V que pretende detetar à lista, com **wsman /** como um prefixo.

## <a name="start-continuous-discovery"></a>Iniciar a deteção contínua

Ligar a partir da aplicação para anfitriões Hyper-V ou clusters e iniciar a deteção VM.

1. Na **nome de utilizador** e **palavra-passe**, especifique as credenciais da conta que a aplicação irá utilizar para detetar VMs. Especifique um nome amigável para as credenciais e clique em **guardar os detalhes**.
2. Clique em **Adicionar anfitrião**e especifique os detalhes do anfitrião/cluster de Hyper-V.
3. Clique em **validar**. Após a validação, é apresentado o número de VMs que podem ser detetados em cada anfitrião/cluster.
    - Se a validação falhar para um anfitrião, rever o erro ao pairar o rato sobre o ícone na **estado** coluna. Corrigir problemas e validar novamente.
    - Para remover anfitriões ou clusters, selecione > **eliminar**.
    - Não é possível remover um anfitrião específico de um cluster. Só é possível remover todo o cluster.
    - Pode adicionar um cluster, mesmo se existirem problemas com a anfitriões específicos do cluster.
4. Após a validação, clique em **guardar e iniciar a deteção** para iniciar o processo de deteção.

Esta ação inicia a deteção. Demora cerca de 15 minutos para os metadados de VMs detetadas a aparecer no portal do Azure. 

## <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a conclusão da deteção, pode verificar se as VMs aparecem no portal.

1. Abra o dashboard do Azure Migrate.
2. Na **do Azure Migrate - servidores** > **do Azure Migrate: Avaliação de servidor** página, clique no ícone que exibe a contagem do **detetados servidores**. 


## <a name="next-steps"></a>Passos seguintes

Experimente [avaliação do Hyper-V](tutorial-assess-hyper-v.md) com avaliação de servidor de migrar do Azure.