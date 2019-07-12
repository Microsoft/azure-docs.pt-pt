---
title: Configurar uma aplicação para o Azure Migrate avaliação/migração do servidor para VMs de VMware | Documentos da Microsoft
description: Descreve como configurar um dispositivo para a deteção, avaliação e migração sem agente de VMs VMware com o Azure Migrate avaliação/migração do servidor.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811729"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurar uma aplicação para VMs de VMware

Este artigo descreve como configurar a aplicação do Azure Migrate, se estiver a avaliar VMware VMs com a ferramenta de avaliação do servidor de migrar do Azure ou, migrar VMs de VMware para o Azure com a migração sem agente utilizando a ferramenta de migração do servidor de migrar do Azure.

A aplicação de VM do VMware é uma simples aplicação utilizada pelo Azure Migrate avaliação/migração do servidor para fazer o seguinte:

- Detete VMs de VMware no local.
- Envie dados de metadados e desempenho para as VMs detetadas para Azure Migrate avaliação/migração do servidor.

[Saiba mais](migrate-appliance.md) sobre a aplicação do Azure Migrate.


## <a name="appliance-deployment-steps"></a>Passos de implementação da aplicação

Para configurar a aplicação da:
- Transferir um ficheiro de modelo OVA e importe-o ao vCenter Server.
- Criar a aplicação e verifique se pode ligar a avaliação do servidor de migrar do Azure. 
- Configurar a aplicação pela primeira vez e registrá-la com o projeto do Azure Migrate.

## <a name="download-the-ova-template"></a>Transferir o modelo de OVA

1. Na **objetivos de migração** > **servidores** > **do Azure Migrate: Avaliação de servidor**, clique em **detetar**.
2. Na **detetar máquinas** > **são as suas máquinas virtualizadas?** , clique em **Sim, com o hipervisor do VMWare vSphere**.
3. Clique em **transferir** para transferir o. Ficheiro de modelo OVA.



### <a name="verify-security"></a>Certifique-se de segurança

Verifique se o ficheiro OVA é seguro, antes de o implementar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Execute o seguinte comando, para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para obter a versão da aplicação 1.0.0.5, o hash gerado deve corresponder a estas definições. 

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>Criar a aplicação da VM

Importar o ficheiro transferido e criar uma VM.

1. Na consola do vSphere Client, clique em **Ficheiro** > **Implementar Modelo OVF**.
2. No Assistente de implementação de modelo OVF > **origem**, especifique a localização do ficheiro OVA.
3. Na **Name** e **localização**, especifique um nome amigável para a VM. Selecione o objeto de inventário em que a VM será alojada.
5. Na **anfitrião/Cluster**, especifique o anfitrião ou cluster no qual a VM será executado.
6. Na **armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
8. Na **mapeamento da rede**, especifique a rede à qual se ligará a VM. A rede necessita de conectividade com a internet, para enviar metadados para a avaliação do servidor de migrar do Azure.
9. Reveja e confirme as definições e, em seguida, clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso da aplicação para o Azure

Certifique-se de que a aplicação da VM pode ligar à [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Configurar a aplicação

Configure a aplicação pela primeira vez.

1. Na consola do vSphere Client, clique com o botão direito do rato na VM > **Abrir Consola**.
2. Fornece o idioma, fuso horário e palavra-passe para a aplicação.
3. Abra um browser em qualquer máquina que pode ligar à VM e abrir o URL da aplicação web da aplicação: **https://*nome da aplicação ou o endereço IP*: 44368**.

   Em alternativa, pode abrir a aplicação no ambiente de trabalho da aplicação ao clicar no atalho de aplicação.
4. Na aplicação web > **configurar pré-requisitos**, efetue o seguinte procedimento:
    - **Licença**: Aceite os termos de licenciamento e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se a VM tem acesso à internet. Se a VM utiliza um proxy:
        - Clique em **definições de Proxy**e especifique o endereço de proxy e porta de escuta, o formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de hora**: Tempo é verificado. A hora da aplicação deve ser sincronizada com o tempo de internet para a deteção funcione corretamente.
    - **Instalar atualizações**: O Azure Migrate verifica se as atualizações mais recentes da aplicação estão instaladas.
    - **Instalar VDDK**: O Azure Migrate verifica que o VMWare vSphere Kit de desenvolvimento de disco Virtual (VDDK) está instalado.
        - Migrates do Azure utiliza o VDDK para replicar máquinas durante a migração para o Azure.
        - Transferir VDDK 6.7 do VMware e extrair os conteúdos do zip transferido para a localização especificada na aplicação.

## <a name="register-the-appliance-with-azure-migrate"></a>Registre-se a aplicação com o Azure Migrate

1. Clique em **iniciar sessão**. Se não aparecer, certifique-se de que ter desabilitado o Bloqueador de pop-up no browser.
2. Na nova guia, inicie sessão com as credenciais do Azure. 
    - Inicie sessão com o nome de utilizador e palavra-passe.
    - Inicie sessão com um PIN não é suportada.
3. Depois de iniciar sessão com êxito na, volte para a aplicação web.
2. Selecione a subscrição na qual foi criado o projeto do Azure Migrate. Em seguida, selecione o projeto.
3. Especifique um nome para a aplicação. O nome deve ser alfanumérica com 14 caracteres ou menos.
4. Clique em **registar**.


## <a name="start-continuous-discovery"></a>Iniciar a deteção contínua

Agora, ligar a partir da aplicação ao vCenter Server e iniciar a deteção de VMS. 

1. Na **especificar detalhes do vCenter Server**, especifique o nome (FQDN) ou o endereço IP do vCenter Server. Pode deixar a porta predefinida ou especificar uma porta personalizada, no qual o vCenter Server aguarda.
2. Na **nome de utilizador** e **palavra-passe**, especifique as credenciais de conta só de leitura que a aplicação irá utilizar para detetar VMs no vCenter server. Certifique-se de que a conta tem o [permissões necessárias para a deteção](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Clique em **validar ligação** para se certificar de que a aplicação pode ligar ao vCenter Server.
4. Depois da ligação é estabelecida, clique em **guardar e iniciar a deteção**.


Esta ação inicia a deteção. Demora cerca de 15 minutos para os metadados de VMs detetadas a aparecer no portal. 


## <a name="next-steps"></a>Passos seguintes

Reveja os tutoriais para [avaliação VMware](tutorial-assess-vmware.md) e [migração sem agente](tutorial-migrate-vmware.md).
