---
title: Preparar o servidor do DPM para efetuar cópias de segurança de cargas de trabalho para o Azure
description: Uma introdução à cópia de segurança de dados do DPM para um cofre dos serviços de recuperação do Azure.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: f119d128b35b93d7e18d514c09d187689d8dffe9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306906"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Preparar a cópia de segurança de cargas de trabalho no Azure com o System Center DPM

Este artigo explica como preparar para cópias de segurança do System Center Data Protection Manager (DPM) para o Azure com o serviço de cópia de segurança do Azure.

O artigo fornece:

- Uma visão geral de implementação do DPM com cópia de segurança do Azure.
- Pré-requisitos e limitações da utilização de cópia de segurança do Azure com o DPM.
- Passos para preparar o Azure, incluindo como configurar um cofre de cópia de segurança de serviços de recuperação e, opcionalmente, modificando o tipo de armazenamento do Azure para o cofre.
- Passos para preparar o servidor do DPM, incluindo a baixar o Cofre de credenciais, instalar o agente de cópia de segurança do Azure e registar o servidor do DPM no cofre.
- Sugestões de resolução de problemas para erros comuns.


## <a name="why-back-up-dpm-to-azure"></a>Por que fazer uma cópia de segurança do DPM para o Azure?

[O System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) faz o backup de dados de ficheiros e aplicativo. O DPM interage com o Azure Backup da seguinte forma:

* **O DPM for executado num servidor físico ou VM no local** — pode fazer backup de dados para um cofre de cópia de segurança no Azure, além para o disco e banda cópia de segurança.
* **O DPM for executado numa VM do Azure** — do System Center 2012 R2 com Update 3 ou posterior, pode implementar o DPM numa VM do Azure. Pode criar cópias de segurança nos discos do Azure ligados à VM ou utilizar o Azure Backup para fazer backup dos dados para um cofre de cópia de segurança.

Os benefícios de negócios cópias de segurança dos servidores do DPM para o Azure incluem:

* Para o DPM no local, o Azure Backup fornece uma alternativa à implementação de longa duração em banda.
* Para o DPM em execução numa VM do Azure, o Azure Backup permite-lhe descarregar o armazenamento do disco do Azure. Armazenamento de dados mais antigos num cofre de cópia de segurança permite-lhe aumentar verticalmente o seu negócio ao armazenar novos dados no disco.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

**Definição** | **Requisito**
--- | ---
DPM numa VM do Azure | System Center 2012 R2 com Update Rollup 3 do DPM 2012 R2 ou posterior.
DPM num servidor físico | System Center 2012 SP1 ou posterior; System Center 2012 R2.
DPM numa VM do Hyper-V | System Center 2012 SP1 ou posterior; System Center 2012 R2.
DPM numa VM do VMware | System Center 2012 R2 com Update Rollup 5 ou posterior.
Componentes | O servidor DPM deve ter o Windows PowerShell e .NET Framework 4.5 instalado.
Aplicações suportadas | [Saiba](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) o que o DPM pode criar cópias de segurança.
Tipos de ficheiro suportados | Estes tipos de ficheiro podem ser uma cópia de segurança com o Azure Backup: Encriptados (cópias de segurança completas só); Comprimidos (cópias de segurança incrementais suportadas); Dispersos (cópias de segurança incrementais suportadas); Comprimidos e dispersos (tratados como dispersos).
Tipos de ficheiro não suportado | Servidores em sistemas de ficheiros sensíveis a maiúsculas; ligações diretas (ignoradas); (ignorados); de pontos de reanálise encriptados e comprimidos (ignorados); encriptados e dispersos (ignorados); Fluxo comprimido; analisar o fluxo.
Armazenamento local | Cada máquina que pretende criar cópias de segurança tem de ter armazenamento local livre é de, pelo menos, 5% do tamanho dos dados que estão a cópia de segurança. Por exemplo, o backup de 100 GB de dados requer um mínimo de 5 GB de espaço livre na localização de rascunho.
Armazenamento do Cofre | Não há limite para a quantidade de dados, que pode criar cópias de segurança para um cofre de cópia de segurança do Azure, mas o tamanho de uma origem de dados (por exemplo uma máquina virtual ou a base de dados) não deve exceder os 54400 GB.
Agente do Backup do Azure | Se o DPM está em execução no System Center 2012 SP1, instale o Rollup 2 ou posterior para o DPM SP1. Isto é necessário para a instalação do agente.<br/><br/> Este artigo descreve como implementar a versão mais recente do agente de cópia de segurança do Azure, também conhecido como o agente do serviço de recuperação do Azure (MARS) da Microsoft. Se tiver uma versão anterior implementada, atualize para a versão mais recente para se certificar que cópia de segurança funciona conforme esperado.

Antes de começar, precisa de uma conta do Azure com a funcionalidade de cópia de segurança do Azure ativada. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Leia sobre [preços de cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modificar as definições de armazenamento

Pode escolher entre o armazenamento com redundância geográfica e armazenamento localmente redundante.

- Por predefinição, o seu cofre tem um armazenamento georredundante.
- Se o Cofre é sua cópia de segurança primária, deixe a opção definida para o armazenamento georredundante. Se pretender que uma opção mais barata que não é tão durável, utilize o procedimento seguinte para configurar o armazenamento localmente redundante.
- Saiba mais sobre [armazenamento do Azure](../storage/common/storage-redundancy.md)e o [georredundante](../storage/common/storage-redundancy-grs.md) e [localmente redundante](../storage/common/storage-redundancy-lrs.md) opções de armazenamento.
- Modifica as definições de armazenamento antes da cópia de segurança inicial. Se já fez backup de um item, pare a realizar cópias de segurança no cofre antes de modificar as definições de armazenamento.

Para editar a definição de replicação de armazenamento:

1. Abra o dashboard do cofre.

2. Na **Manage**, clique em **infraestrutura de cópia de segurança**.

3. Na **configuração de cópia de segurança** menu, selecione um armazenamento de opção para o cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Transferir as credenciais do cofre

Utilize as credenciais do cofre quando registar o servidor do DPM no cofre.

- O ficheiro de credenciais do cofre é um certificado gerado pelo portal para cada cofre de cópia de segurança.
- O portal, em seguida, carrega a chave pública para o Access Control Service (ACS).
- Durante o fluxo de trabalho do registo de máquina, chave privada do certificado é disponibilizada para o usuário, que autentica a máquina.
- Base a autenticação, o serviço de cópia de segurança do Azure envia dados para o Cofre identificado.

### <a name="best-practices-for-vault-credentials"></a>Melhores práticas para as credenciais do Cofre

Para obter as credenciais de transferir o ficheiro de credenciais de cofre através de um canal seguro do portal do Azure:

- As credenciais do cofre são utilizadas apenas durante o fluxo de trabalho do registo.
- É da responsabilidade do cliente, certifique-se de que o ficheiro de credenciais do Cofre é não comprometido e seguro.
    - Se se perder o controlo sobre as credenciais, as credenciais do cofre podem ser utilizadas para registar outras máquinas para o cofre.
    - No entanto, os dados de cópia de segurança são encriptados com uma frase de acesso que pertence ao cliente, para que os dados de cópia de segurança existentes não podem ficar comprometidos.
- Certifique-se de que esse ficheiro é guardado numa localização que pode ser acedida a partir do servidor DPM. Se ele é armazenado num compartilhamento de arquivo/SMB, verifique as permissões de acesso.
- As credenciais do cofre expiram após 48 horas. Pode transferir as credenciais do cofre novo quantas vezes for necessário. No entanto, apenas o ficheiro de credenciais de cofre mais recente pode ser utilizado durante o fluxo de trabalho do registo.
- O serviço de cópia de segurança do Azure não está ciente de chave privada do certificado e a chave privada não está disponível no portal do ou o serviço.

Transferi o ficheiro de credenciais do cofre para um computador local da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra o Cofre no qual pretende registar o servidor DPM.
3. Na **configurações**, clique em **propriedades**.

    ![Abrir o menu do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Na **propriedades** > **credenciais de cópia de segurança**, clique em **transferir**. O portal gera o arquivo de credencial de cofre utilizando uma combinação da data atual e o nome do cofre e torna-o disponível para download.

    ![Transferência](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Clique em **salvar** para transferir as credenciais do cofre para a pasta, ou **guardar como** e especifique uma localização. Irá demorar até um minuto para o ficheiro ser gerado.


## <a name="install-the-backup-agent"></a>Instalar o agente de cópia de segurança

Todas as máquinas que é uma cópia de segurança pelo Azure Backup tem de ter o agente de cópia de segurança (também conhecido como o agente do serviço de recuperação do Azure (MARS) da Microsoft) instalado no mesmo. Instale o agente no servidor do DPM da seguinte forma:

1. Abra o Cofre ao qual pretende registar o servidor DPM.
2. Na **configurações**, clique em **propriedades**.

    ![Abrir o menu do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Sobre o **propriedades** página, transfira o agente de cópia de segurança do Azure.

    ![Transferência](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. Depois de baixar, execute MARSAgentInstaller.exe. Para instalar o agente no computador do DPM.
5. Selecione uma pasta de instalação e a pasta de cache para o agente. O espaço livre de localização de cache tem de ser, pelo menos, 5% dos dados de cópia de segurança.
6. Se utilizar um servidor proxy para ligar à internet, o **configuração do Proxy** ecrã, introduza os detalhes do servidor proxy. Se utilizar um proxy autenticado, introduza os detalhes de nome e palavra-passe do utilizador neste ecrã.
7. O agente de cópia de segurança do Azure instala o .NET Framework 4.5 e o Windows PowerShell (se não estiver instalados) para concluir a instalação.
8. Depois do agente está instalado, **fechar** a janela.

    ![Fechar](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registar o servidor do DPM no Cofre

1. Na consola do administrador do DPM > **gerenciamento**, clique em **Online**. Selecione **Registar**. Isso abrirá o Assistente de registo do servidor.
2. Na **configuração do Proxy**, especifique as definições de proxy conforme necessário.

    ![Configuração de proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Na **Cofre de cópia de segurança**, procure e selecione o ficheiro de credenciais de cofre que transferiu.

    ![Credenciais do Cofre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. Na **definição de limitação**, opcionalmente, pode ativar a limitação para cópias de segurança de largura de banda. Pode definir os limites de velocidade para especificar as horas de trabalho e dias.

    ![Definição de limitação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. Na **definição da pasta de recuperação**, especifique uma localização que pode ser utilizada durante a recuperação de dados.

    - O Azure Backup utiliza esta localização como uma área de armazenamento temporário para dados recuperados.
    - Após a recuperação de dados terminar, cópia de segurança do Azure irá limpar os dados nesta área.
    - A localização tem de ter espaço suficiente para reter os itens que antecipa recuperar em paralelo.

    ![Definição da pasta de recuperação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. Na **definição de encriptação** gerar ou forneça uma frase de acesso.

    - A frase de acesso é utilizado para encriptar as cópias de segurança para a cloud.
    - Especifica um mínimo de 16 carateres.
    - Guardar o ficheiro numa localização segura, é necessário para recuperação.

    ![Encriptação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Que possui a frase de acesso de encriptação e a Microsoft não tem visibilidade para o mesmo.
    > Se a frase de acesso for perdida ou esquecida; Microsoft não pode ajudar a recuperar os dados de cópia de segurança.

13. Clique em **registar** para registar o servidor do DPM no cofre.

Depois do servidor está registado com êxito para o Cofre e agora está pronto para iniciar o backup do Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>Resolver problemas relacionados com as credenciais do Cofre

### <a name="expiration-error"></a>Erro de expiração

O ficheiro de credenciais do cofre só é válido para 48 horas (depois de ser transferido a partir do portal). Se encontrar qualquer erro neste ecrã (por exemplo, "as credenciais do cofre ficheiro fornecido expirou"), o início de sessão para o portal do Azure e a transferência que as credenciais do cofre ficheiro novamente.

### <a name="access-error"></a>Erro de acesso

Certifique-se de que o ficheiro de credenciais do Cofre está disponível num local que pode ser acedido pelo aplicativo de instalação. Se ocorrerem erros relacionados de acesso, copie o ficheiro de credenciais do cofre para uma localização temporária nesta máquina e repita a operação.

### <a name="invalid-credentials-error"></a>Erro de credenciais inválidas

Se ocorrer um erro de credenciais de cofre inválidas (por exemplo, "Cofre inválidas as credenciais fornecidas") o ficheiro está danificado ou não ter as credenciais mais recentes associadas ao serviço de recuperação.

- Repita a operação depois de baixar um novo ficheiro de credenciais do cofre a partir do portal.
- Este erro normalmente é visto quando clica no **credenciais do Cofre de Download** opção no portal do Azure, duas vezes numa rápida sucessão. Neste caso, apenas o ficheiro de credenciais de cofre segundo é válido.
