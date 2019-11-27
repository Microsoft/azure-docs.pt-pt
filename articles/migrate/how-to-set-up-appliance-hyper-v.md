---
title: Configurar um dispositivo de migrações para Azure para Hyper-V
description: Saiba como configurar um dispositivo de migrações para Azure para avaliar e migrar VMs do Hyper-V.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: a94d11d48728b03dd978af85db4b6c2af4887938
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534496"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurar um dispositivo para VMs do Hyper-V

Este artigo descreve como configurar o dispositivo de migrações para Azure se você estiver avaliando VMs do Hyper-V com a ferramenta de avaliação de servidor migrações para Azure ou migrando VMs VMware para o Azure usando a ferramenta de migração de servidor migrações para Azure.

O dispositivo de VM do Hyper-V é um dispositivo leve usado pela migração/avaliação do servidor de migrações para Azure para fazer o seguinte:

- Descubra VMs do Hyper-V locais.
- Enviar dados de desempenho e metadados para VMs descobertas para migração/avaliação de servidor migrar do Azure.

[Saiba mais](migrate-appliance.md) sobre o dispositivo migrações para Azure.


## <a name="appliance-deployment-steps"></a>Etapas de implantação do dispositivo

Para configurar o dispositivo, você:
- Baixe um VHD do Hyper-V compactado da portal do Azure.
- Crie o dispositivo e verifique se ele pode se conectar à avaliação do servidor de migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure.

## <a name="download-the-vhd"></a>Baixar o VHD

Baixe o modelo de VHD compactado para o dispositivo.

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, clique em **descobrir**.
2. Em **descobrir computadores** > **são seus computadores virtualizados?** , clique em **Sim, com o Hyper-V**.
3. Clique em **baixar** para baixar o arquivo VHD.

    ![Baixar VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar segurança

Verifique se o arquivo compactado é seguro, antes de implantá-lo.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Execute o comando a seguir para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  Para a versão do dispositivo 2.19.11.12, o hash gerado deve corresponder a essas configurações.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31



## <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie a VM.

1. Extraia o arquivo VHD compactado em uma pasta no host Hyper-V que hospedará a VM do dispositivo. Três pastas são extraídas.
2. Abra o Gerenciador do Hyper-V. Em **ações**, clique em **importar máquina virtual**.

    ![Implantar VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. No assistente de importação de máquina virtual > **antes de começar**, clique em **Avançar**.
3. Em **Localizar pasta**, especifique a pasta que contém o VHD extraído. Clique depois em **Seguinte**.
1. Em **selecionar máquina virtual**, clique em **Avançar**.
2. Em **escolher tipo de importação**, clique em **copiar a máquina virtual (criar uma nova ID exclusiva)** . Clique depois em **Seguinte**.
3. Em **escolher destino**, deixe a configuração padrão. Clique em **Seguinte**.
4. Em **pastas de armazenamento**, deixe a configuração padrão. Clique em **Seguinte**.
5. Em **escolher rede**, especifique o comutador virtual que será usado pela VM. A opção precisa de conectividade com a Internet para enviar dados para o Azure.
6. Em **Resumo**, examine as configurações. Em seguida, clique em **concluir**.
7. No Gerenciador do Hyper-V > **máquinas virtuais**, inicie a VM.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. No Gerenciador do Hyper-V > **máquinas virtuais**, clique com o botão direito do mouse na VM > **conectar**.
2. Forneça o idioma, o fuso horário e a senha para o dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
1. No aplicativo Web > **configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
        - Clique em **configurações de proxy**e especifique o endereço de proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de horário**: o tempo é verificado. O tempo no dispositivo deve ser sincronizado com o horário da Internet para que a descoberta da VM funcione corretamente.
    - **Instalar atualizações**: avaliação do servidor de migrações para Azure verifica se o dispositivo tem as atualizações mais recentes instaladas.

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

Se você estiver executando VHDs em SMBs, deverá habilitar a delegação de credenciais do dispositivo para os hosts do Hyper-V. Para fazer isso no dispositivo:

1. Na VM do dispositivo, execute este comando. HyperVHost1/HyperVHost2 são nomes de host de exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Como alternativa, faça isso na Editor de Política de Grupo Local no dispositivo:
    - Em **política do computador Local** > **configuração do computador**, clique em **modelos administrativos** > **sistema** > **delegação de credenciais**.
    - Clique duas vezes em **Permitir Delegação de novas credenciais**e selecione **habilitado**.
    - Em **Opções**, clique em **Mostrar**e adicione cada host Hyper-V que você deseja descobrir à lista, com **WSMan/** como um prefixo.
    - Em **delegação de credenciais**, clique duas vezes em **Permitir Delegação de novas credenciais com autenticação de servidor somente NTML**. Novamente, adicione cada host Hyper-V que você deseja descobrir à lista, com **WSMan/** como um prefixo.

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

## <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a conclusão da descoberta, você pode verificar se as VMs aparecem no Portal.

1. Abra o painel migrações para Azure.
2. Na página **migrações para Azure – servidores** > **migrações para Azure: avaliação do servidor** , clique no ícone que exibe a contagem de **servidores descobertos**.


## <a name="next-steps"></a>Passos seguintes

Experimente a [avaliação do Hyper-V](tutorial-assess-hyper-v.md) com a avaliação do servidor de migrações para Azure.
