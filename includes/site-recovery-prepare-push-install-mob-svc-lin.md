---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5ba55e339db4c33d1b0d759e4682481e20318938
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203734"
---
### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Preparar uma instalação por push num servidor Linux

1. Certifique-se de que existe conectividade de rede entre o computador com Linux e o servidor de processos.
1. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ser um utilizador **raiz** no servidor Linux de origem. Utilize esta conta apenas para a instalação de push e nas atualizações.
1. Verifique se o ficheiro /etc/hosts no servidor Linux de origem tem entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
1. Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que quer replicar.
1. Certifique-se de que o Secure Shell (SSH) está ativado e em execução na porta 22.
1. Ative a autenticação de subsistema e palavra-passe SFTP no ficheiro sshd_config. Siga estes passos.

    a. Inicie sessão como **raiz**.

    b. Na **/etc/ssh/sshd_config** ficheiro, encontre a linha que começa com **PasswordAuthentication**.

    c. Anule os comentários da linha e altere o valor para **Sim**.

    d. Encontre a linha que começa com **subsistema**, e anule os comentários da linha.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Reinicie o serviço **sshd**.

1. Adicione a conta que criou no CSPSConfigtool. Siga estes passos.

    a. Inicie sessão no servidor de configuração.

    b. Abra **cspsconfigtool.exe**. Está disponível como um atalho na área de trabalho e na pasta %programdata%\home\svsystems\bin.

    c. Sobre o **gerir contas** separador, selecione **adicionar conta**.

    d. Adicione a conta que criou.

    d. Introduza as credenciais que utiliza para ativar a replicação para um computador.
