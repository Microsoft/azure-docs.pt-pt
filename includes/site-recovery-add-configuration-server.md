---
title: incluir ficheiro
description: incluir ficheiro
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: f699ffe6d5a91e8ce3ae90c7e12249bbad0fff3e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001571"
---
1. Execute o ficheiro de instalação Configuração Unificada.
2. Antes **de começar**, selecione **Instale o servidor de configuração e o servidor de processo**.

    ![Screenshot do ecrã Antes de Começar na Configuração Unificada.](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. Em **Licença de Software de Terceiros**, clique em **Aceito**, para transferir e instalar o MySQL.

    ![Screenshot do ecrã da licença de software de terceiros em Configuração Unificada.](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. Em **Registo**, selecione a chave de registo que transferiu a partir do cofre.

    ![Screenshot do ecrã de registo em Configuração Unificada.](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Em **Definições da Internet**, especifique a forma como o Fornecedor em execução no servidor estabelece ligação ao Azure Site Recovery através da Internet. Certifique-se de ter permitido os URLs necessários.

    - Se pretender ligar-se ao proxy que está atualmente configurado na máquina, selecione **Connect to Azure Site Recovery utilizando um servidor proxy**.
    - Se pretender que o Fornecedor se conecte diretamente, selecione **Connect diretamente para a Recuperação do Site Azure sem um servidor proxy**.
    - Se o representante existente necessitar de autenticação, ou se pretender utilizar um representante personalizado para a ligação do Fornecedor, selecione **Connect com configurações de procuração personalizadas** e especifique o endereço, porta e credenciais.
     ![Screenshot do ecrã de Definições de Internet na Configuração Unificada.](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Em **Verificação de Pré-requisitos**, a Configuração executa uma verificação para se certificar de que a instalação pode ser executada. Se for apresentado um aviso sobre a **Verificação de sincronização de hora global**, certifique-se de que a hora no relógio do sistema (definições de **Data e Hora**) é a mesma que o fuso horário.

    ![Screenshot do ecrã de verificação pré-requisitos na configuração unificada.](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. Em **Configuração do MySQL**, crie as credenciais para iniciar sessão na instância de servidor do MySQL que está instalada.

    ![Screenshot do ecrã de configuração MySQL em Configuração Unificada.](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. Em **Detalhes ambientais**, selecione Não se estiver a replicar VMs da Azure Stack ou servidores físicos. 
9. Em **Localização de Instalação**, selecione onde pretende instalar os binários e armazenar a cache. A unidade que selecionar tem de ter, pelo menos, 5 GB de espaço disponível no disco, mas recomenda-se uma unidade de cache com, pelo menos, 600 GB de espaço livre.

    ![Screenshot do ecrã de localização de instalação na configuração unificada.](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. Na **Seleção de Rede**, selecione primeiro o NIC que o servidor de processo incorporado utiliza para a descoberta e a instalação de serviço de mobilidade em máquinas de origem e, em seguida, selecione o NIC que o Servidor de Configuração utiliza para conectividade com o Azure. A porta 9443 é a porta predefinida utilizada para envio e receção de tráfego de replicação, mas pode modificar este número de porta para melhor corresponda às necessidades do seu ambiente. Além da porta 9443, podemos também abrir a porta 443, que é utilizada por um servidor Web para orquestrar operações de replicação. Não utilize a porta 443 para enviar ou receber tráfego de replicação.

    ![Screenshot do ecrã de seleção de rede em Configuração Unificada.](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Em **Resumo**, consulte as informações e clique em **Instalar**. Quando a instalação estiver concluída, é gerada uma frase de acesso. Irá precisar dela, quando ativar a replicação, por isso, copie-a e mantenha-a numa localização segura.

    ![Screenshot do ecrã Resumo em Configuração Unificada.](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Após o início do registo, o servidor é apresentado na lâmina **'Servidores' de Definições**  >  **Servers** no cofre.
