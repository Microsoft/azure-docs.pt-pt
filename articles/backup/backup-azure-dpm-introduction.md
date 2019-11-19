---
title: Preparar o servidor DPM para fazer backup de cargas de trabalho
description: Neste artigo, saiba como preparar os backups do System Center Data Protection Manager (DPM) para o Azure usando o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: c7b662c07caecb7871ec9f8be89ffc76b8f7f5fb
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173142"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Preparar para fazer backup de cargas de trabalho no Azure com o System Center DPM

Este artigo explica como preparar os backups do System Center Data Protection Manager (DPM) para o Azure, usando o serviço de backup do Azure.

O artigo fornece:

- Uma visão geral da implantação do DPM com o backup do Azure.
- Pré-requisitos e limitações para usar o backup do Azure com o DPM.
- Etapas para preparar o Azure, incluindo a configuração de um cofre de backup dos serviços de recuperação e, opcionalmente, a modificação do tipo de armazenamento do Azure para o cofre.
- Etapas para preparar o servidor DPM, incluindo o download de credenciais do cofre, a instalação do agente de backup do Azure e o registro do servidor DPM no cofre.
- Dicas de solução de problemas para erros comuns.

## <a name="why-back-up-dpm-to-azure"></a>Por que fazer backup do DPM no Azure?

[O System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) faz backup de dados de arquivos e aplicativos. O DPM interage com o backup do Azure da seguinte maneira:

- **DPM em execução em um servidor físico ou VM local** — você pode fazer backup de dados em um cofre de backup no Azure, além de backup em disco e fita.
- **DPM em execução em uma VM do Azure** — do System Center 2012 R2 com atualização 3 ou posterior, você pode implantar o DPM em uma VM do Azure. Você pode fazer backup de dados em discos do Azure anexados à VM ou usar o backup do Azure para fazer backup dos dados em um cofre de backup.

Os benefícios comerciais de fazer backup de servidores DPM para o Azure incluem:

- Para o DPM local, o backup do Azure fornece uma alternativa à implantação de longo prazo em fita.
- Para o DPM em execução em uma VM do Azure, o backup do Azure permite descarregar o armazenamento do disco do Azure. O armazenamento de dados mais antigos em um cofre de backup permite que você amplie seus negócios armazenando novos dados no disco.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

**Definição** | **Requisito**
--- | ---
DPM em uma VM do Azure | System Center 2012 R2 com o DPM 2012 R2 pacote cumulativo de atualizações 3 ou posterior.
DPM em um servidor físico | System Center 2012 SP1 ou posterior; System Center 2012 R2.
DPM em uma VM do Hyper-V | System Center 2012 SP1 ou posterior; System Center 2012 R2.
DPM em uma VM VMware | System Center 2012 R2 com pacote cumulativo de atualizações 5 ou posterior.
Componentes | O servidor DPM deve ter o Windows PowerShell e o .NET Framework 4,5 instalados.
Aplicativos com suporte | [Saiba](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) o que o DPM pode fazer backup.
Tipos de arquivo com suporte | É possível fazer backup desses tipos de arquivo com o backup do Azure: criptografado (somente backups completos); Compactado (backups incrementais com suporte); Esparso (backups incrementais com suporte); Compactados e esparsos (tratados como esparsos).
Tipos de arquivo sem suporte | Servidores em sistemas de arquivos que diferenciam maiúsculas de minúsculas; links físicos (ignorados); pontos de nova análise (ignorados); criptografado e compactado (ignorado); criptografado e esparso (ignorado); Fluxo compactado; analisar fluxo.
Armazenamento local | Cada computador que você deseja fazer backup deve ter armazenamento local livre que tenha pelo menos 5% do tamanho dos dados que estão sendo submetidos a backup. Por exemplo, o backup de 100 GB de dados requer um mínimo de 5 GB de espaço livre no local de rascunho.
Armazenamento do cofre | Não há limite para a quantidade de dados que você pode fazer backup em um cofre de backup do Azure, mas o tamanho de uma fonte de dados (por exemplo, uma máquina virtual ou banco de dado) não deve exceder 54.400 GB.
Azure ExpressRoute | Se o Azure ExpressRoute estiver configurado com o emparelhamento privado ou da Microsoft, ele não poderá ser usado para fazer backup dos dados no Azure.<br/><br/> Se o Azure ExpressRoute estiver configurado com o emparelhamento público, ele poderá ser usado para fazer backup dos dados no Azure.<br/><br/> **Observação:** O emparelhamento público foi preterido para novos circuitos.
Agente do Backup do Azure | Se o DPM estiver em execução no System Center 2012 SP1, instale o pacote cumulativo de atualizações 2 ou posterior para o DPM SP1. Isso é necessário para a instalação do agente.<br/><br/> Este artigo descreve como implantar a versão mais recente do agente de backup do Azure, também conhecido como agente MARS (Microsoft Azure Recovery Service). Se você tiver uma versão anterior implantada, atualize para a versão mais recente para garantir que o backup funcione conforme o esperado.

Antes de começar, você precisa de uma conta do Azure com o recurso de backup do Azure habilitado. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Leia sobre os [preços do backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modificar configurações de armazenamento

Você pode escolher entre o armazenamento com redundância geográfica e o armazenamento com redundância local.

- Por predefinição, o seu cofre tem um armazenamento georredundante.
- Se o cofre for o seu backup primário, deixe a opção definida como armazenamento com redundância geográfica. Se você quiser uma opção mais barata que não seja tão durável, use o procedimento a seguir para configurar o armazenamento com redundância local.
- Saiba mais sobre o [armazenamento do Azure](../storage/common/storage-redundancy.md)e as opções de armazenamento [com redundância geográfica e com](../storage/common/storage-redundancy-grs.md) redundância [local](../storage/common/storage-redundancy-lrs.md) .
- Modifique as configurações de armazenamento antes do backup inicial. Se você já tiver feito backup de um item, pare de fazer o backup no cofre antes de modificar as configurações de armazenamento.

Para editar a definição de replicação de armazenamento:

1. Abra o painel do cofre.

2. Em **gerenciar**, clique em **infraestrutura de backup**.

3. No menu **configuração de backup** , selecione uma opção de armazenamento para o cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Transferir as credenciais do cofre

Você usa credenciais do cofre ao registrar o servidor DPM no cofre.

- O ficheiro de credenciais do cofre é um certificado gerado pelo portal para cada cofre de cópia de segurança.
- O portal, em seguida, carrega a chave pública para o Access Control Service (ACS).
- Durante o fluxo de trabalho de registro da máquina, a chave privada do certificado é disponibilizada para o usuário, que autentica a máquina.
- Com base na autenticação, o serviço de backup do Azure envia dados para o cofre identificado.

### <a name="best-practices-for-vault-credentials"></a>Práticas recomendadas para credenciais do cofre

Para obter as credenciais, baixe o arquivo de credencial do cofre por meio de um canal seguro do portal do Azure:

- As credenciais do cofre são usadas somente durante o fluxo de trabalho de registro.
- É sua responsabilidade garantir que o arquivo de credenciais do cofre seja seguro e não seja comprometido.
  - Se o controle das credenciais for perdido, as credenciais do cofre poderão ser usadas para registrar outros computadores no cofre.
  - No entanto, os dados de backup são criptografados usando uma frase secreta que pertence ao cliente, portanto, os dados de backup existentes não podem ser comprometidos.
- Certifique-se de que o arquivo seja salvo em um local que possa ser acessado pelo servidor DPM. Se ele estiver armazenado em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.
- As credenciais do cofre expiram após 48 h. Você pode baixar novas credenciais do cofre quantas vezes forem necessárias. No entanto, somente o arquivo de credencial mais recente do cofre pode ser usado durante o fluxo de trabalho de registro.
- O serviço de backup do Azure não reconhece a chave privada do certificado e a chave privada não está disponível no portal ou no serviço.

Baixe o arquivo de credenciais do cofre em um computador local da seguinte maneira:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra o cofre no qual você deseja registrar o servidor DPM.
3. Em **configurações**, clique em **Propriedades**.

    ![Abrir o menu do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Em **propriedades** > **credenciais de backup**, clique em **baixar**. O portal gera o arquivo de credencial do cofre usando uma combinação do nome do cofre e da data atual e o torna disponível para download.

    ![Transferência](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Clique em **salvar** para baixar as credenciais do cofre para a pasta ou em **salvar como** e especifique um local. Levará até um minuto para que o arquivo seja gerado.

## <a name="install-the-backup-agent"></a>Instalar o agente de backup

Cada computador que é submetido a backup pelo backup do Azure deve ter o agente de backup (também conhecido como o agente MARS (Microsoft Azure Recovery Service) instalado. Instale o agente no servidor DPM da seguinte maneira:

1. Abra o cofre no qual você deseja registrar o servidor DPM.
2. Em **configurações**, clique em **Propriedades**.

    ![Abrir o menu do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na página **Propriedades** , baixe o agente de backup do Azure.

    ![Transferência](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Após o download, execute MARSAgentInstaller. exe. para instalar o agente no computador do DPM.
5. Selecione uma pasta de instalação e uma pasta de cache para o agente. O espaço livre do local do cache deve ser pelo menos 5% dos dados de backup.
6. Se você usar um servidor proxy para se conectar à Internet, na tela **configuração de proxy** , insira os detalhes do servidor proxy. Se você usar um proxy autenticado, insira os detalhes de nome de usuário e senha nesta tela.
7. O agente de backup do Azure instala o .NET Framework 4,5 e o Windows PowerShell (se não estiverem instalados) para concluir a instalação.
8. Depois que o agente for instalado, **feche** a janela.

    ![Fechar](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrar o servidor DPM no cofre

1. No console do administrador do DPM > **Gerenciamento**, clique em **online**. Selecione **Registar**. O assistente para registrar servidor será aberto.
2. Em **configuração de proxy**, especifique as configurações de proxy conforme necessário.

    ![Configuração de proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. Em **cofre de backup**, navegue até e selecione o arquivo de credenciais do cofre que você baixou.

    ![Credenciais do cofre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. Na **configuração de limitação**, você pode opcionalmente habilitar a limitação de largura de banda para backups. Você pode definir os limites de velocidade para especificar horas de trabalho e dias.

    ![Configuração de limitação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. Em **pasta de recuperação configuração**, especifique um local que possa ser usado durante a recuperação de dados.

    - O backup do Azure usa esse local como uma área de manutenção temporária para dados recuperados.
    - Depois de concluir a recuperação de dados, o backup do Azure limpará os dados nessa área.
    - O local deve ter espaço suficiente para manter os itens que você prevê que estão sendo recuperados em paralelo.

    ![Configuração da pasta de recuperação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. Em **configuração de criptografia**, gere ou forneça uma frase secreta.

    - A frase secreta é usada para criptografar os backups para a nuvem.
    - Especifique um mínimo de 16 caracteres.
    - Salve o arquivo em um local seguro, ele é necessário para a recuperação.

    ![Encriptação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Você possui a senha de criptografia e a Microsoft não tem visibilidade sobre ela.
    > Se a frase secreta for perdida ou esquecida; A Microsoft não pode ajudar a recuperar os dados de backup.

7. Clique em **registrar** para registrar o servidor DPM no cofre.

Depois que o servidor é registrado com êxito no cofre e agora você está pronto para iniciar o backup em Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>Solucionar problemas de credenciais do cofre

### <a name="expiration-error"></a>Erro de expiração

O arquivo de credenciais do cofre é válido somente por 48 h (após ser baixado do Portal). Se você encontrar algum erro nessa tela (por exemplo, "o arquivo de credenciais do cofre fornecido expirou"), faça logon no portal do Azure e baixe o arquivo de credenciais do cofre novamente.

### <a name="access-error"></a>Erro de acesso

Verifique se o arquivo de credenciais do cofre está disponível em um local que pode ser acessado pelo aplicativo de instalação. Se você encontrar erros relacionados ao acesso, copie o arquivo de credenciais do cofre para um local temporário neste computador e repita a operação.

### <a name="invalid-credentials-error"></a>Erro de credenciais inválido

Se você encontrar um erro de credencial de cofre inválido (por exemplo, "credenciais de cofre fornecidas inválidas"), o arquivo estará corrompido ou não terá as credenciais mais recentes associadas ao serviço de recuperação.

- Repita a operação depois de baixar um novo arquivo de credencial de cofre do Portal.
- Esse erro normalmente é visto quando você clica na opção **baixar credencial do cofre** na portal do Azure, duas vezes em sucessão rápida. Nesse caso, somente o arquivo de credencial do segundo cofre é válido.
