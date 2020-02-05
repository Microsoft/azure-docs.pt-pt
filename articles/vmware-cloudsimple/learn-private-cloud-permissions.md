---
title: Soluções Azure VMware (AVS) - Modelo de permissão de permissão AVS Private Cloud
description: Descreve o modelo de permissão de permissão, grupos e categorias de permissão DaVs Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9488c59ead23fb68633ccc56a0df905ebfeea079
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014951"
---
# <a name="avs-private-cloud-permission-model-of-vmware-vcenter"></a>Modelo de permissão de nuvem privada AVS do VMware vCenter

A AVS mantém acesso administrativo total ao ambiente DaVs Private Cloud. A cada cliente AVS é concedido privilégios administrativos suficientes para poder implantar e gerir as máquinas virtuais no seu ambiente. Se necessário, você pode escalonar seus privilégios temporariamente para executar funções administrativas.

## <a name="cloud-owner"></a>Proprietário da nuvem

Ao criar uma Nuvem Privada AVS, um utilizador **cloudOwner** é criado no domínio de sign-on single vCenter, com acesso **Cloud-Owner-Role** para gerir objetos na Nuvem Privada AVS. Este utilizador também pode configurar fontes de [identidade vCenter](set-vcenter-identity.md)adicionais, e outros utilizadores para o VCenter De Nuvem Privada AVS.

> [!NOTE]
> O utilizador padrão para o seu VCenter De Nuvem Privada AVS é cloudowner@AVS.local quando é criada uma Nuvem Privada AVS.

## <a name="user-groups"></a>Grupos de Utilizadores

Um grupo chamado **Cloud-Owner-Group** é criado durante a implantação de uma Nuvem Privada AVS. Os utilizadores deste grupo podem administrar várias partes do ambiente vSphere na Nuvem Privada AVS. Esse grupo recebe automaticamente os privilégios de **função de proprietário da nuvem** e o usuário **CloudOwner** é adicionado como um membro desse grupo. A AVS cria grupos adicionais com privilégios limitados para facilitar a gestão. Você pode adicionar qualquer usuário a esses grupos criados previamente e os privilégios definidos abaixo são automaticamente atribuídos aos usuários nos grupos.

### <a name="pre-created-groups"></a>Grupos pré-criados

| Nome do grupo | Finalidade | Função |
| -------- | ------- | ------ |
| Grupo de proprietário da nuvem | Membros deste grupo têm privilégios administrativos para o AVS Private Cloud vCenter | [Nuvem-proprietário-função](#cloud-owner-role) |
| Cloud-global-cluster-admin-Group | Membros deste grupo têm privilégios administrativos no Cluster AVS Private Cloud vCenter | [Cloud-cluster-admin-role](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-admin-Group | Membros deste grupo podem gerir o armazenamento no AVS Private Cloud vCenter | [Cloud-Storage-admin-role](#cloud-storage-admin-role) |
| Cloud-Global-Network-admin-Group | Os membros deste grupo podem gerir a rede e distribuir grupos portuários no AVS Private Cloud vCenter | [Cloud-Network-admin-role](#cloud-network-admin-role) |
| Cloud-global-VM-admin-Group | Membros deste grupo podem gerir máquinas virtuais no VCenter De Nuvem Privada AVS | [Cloud-VM-admin-role](#cloud-vm-admin-role) |

Para conceder permissões individuais aos utilizadores para gerir a Nuvem Privada AVS, crie contas de utilizador adicionando aos grupos apropriados.

> [!CAUTION]
> Novos usuários devem ser adicionados somente a *Cloud-Owner-Group*, *Cloud-global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* ou, *Cloud-global-VM-admin-Group*.  Os usuários adicionados ao grupo de *Administradores* serão removidos automaticamente.  Somente as contas de serviço devem ser adicionadas ao grupo de *Administradores* e as contas de serviço não devem ser usadas para entrar na interface do usuário da Web do amvSphere.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lista de privilégios do vCenter para funções padrão

### <a name="cloud-owner-role"></a>Nuvem-proprietário-função

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Alarmes** | Alarme de reconhecimento <br> Criar alarme <br> Desabilitar ação de alarme <br> Modificar alarme <br> Remover alarme <br> Definir status do alarme |
| **Permissões** | Modificar permissão |
| **Biblioteca de conteúdo** | Adicionar item de biblioteca <br> Criar biblioteca local <br> Criar biblioteca inscrita <br> Excluir item de biblioteca <br> Excluir biblioteca local <br> Excluir biblioteca assinada <br> Transferir ficheiros <br> Remover item de biblioteca <br> Remover biblioteca assinada <br> Importar armazenamento <br> Informações de assinatura de investigação <br> Armazenamento de leitura <br> Sincronizar item de biblioteca <br> Sincronizar biblioteca assinada <br> Introspecção de tipo <br> Atualizar definições de configuração <br> Arquivos de atualização <br> Atualizar biblioteca <br> Atualizar item de biblioteca <br> Atualizar biblioteca local <br> Atualizar biblioteca assinada <br> Exibir definições de configuração |
| **Operações criptográficas** | Adicionar disco <br> Clone <br> Desencriptar <br> Acesso Direto <br> Encriptar <br> Criptografar novo <br> Gerenciar KMS <br> Gerenciar políticas de criptografia <br> Gerir chaves <br> Migração <br> Recriptografado <br> Registrar VM <br> Registrar host |
| **grupo de dvPort** | Create <br> Eliminar <br> Modificar <br> Operação de política <br> Operação de escopo |
| **Repositório** | Alocar espaço <br> Procurar repositório de armazenamento <br> Configurar repositório de armazenamento <br> Operações de arquivo de nível baixo <br> Mover repositório de armazenamento <br> Remover repositório de armazenamento <br> Remover arquivo <br> Renomear repositório de armazenamento <br> Atualizar arquivos de máquina virtual <br> Atualizar metadados da máquina virtual |
| **Gerenciador de Agentes ESX** | Config <br> Modificar <br> Vista |
| **Extensão** | Registrar extensão <br> Cancelar registro da extensão <br> Atualizar extensão |
| **Provedor de estatísticas externas**| Registar <br> Anular o Registo <br> Atualizar |
| **Pasta** | Criar pasta <br> Excluir pasta <br> Mover pasta <br> Renomear pasta |
| **Geral** | Cancelar tarefa <br> Planeamento de capacidade <br> Diagnósticos <br> Desabilitar métodos <br> Habilitar métodos <br> Marca global <br> Saúde <br> Licenças <br> Evento de log <br> Gerenciar atributos personalizados <br> Proxy <br> Ação de script <br> Gerenciadores de serviços <br> Definir atributo personalizado <br> Marca do sistema |
| **Provedor de atualização de integridade** | Registar <br> Anular o Registo <br> Atualizar |
| **Configuração de > de host** | Configuração da partição de armazenamento |
| **Inventário de > de host** | Modificar cluster |
| **Marcação de vSphere** | Atribuir ou cancelar a atribuição de marca vSphere <br> Criar marca vSphere <br> Criar categoria de marca vSphere <br> Excluir marca vSphere <br> Excluir categoria de marca vSphere <br> Editar marca vSphere <br> Editar categoria da marca vSphere <br> Modificar o campo UsedBy para a categoria <br> Modificar campo UsedBy para marca |
| **Rede** | Atribuir rede <br> Configurar <br> Mover rede <br> Remover |
| **Performance** (Desempenho) | Modificar intervalos |
| **Perfil de host** | Vista |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao pool de recursos <br> Atribuir máquina virtual ao pool de recursos <br> Criar pool de recursos <br> Migrar máquina virtual desligada <br> Migrar máquina virtual ligada <br> Modificar pool de recursos <br> Mover pool de recursos <br> Consultar o vMotion <br> Remover pool de recursos <br> Renomear pool de recursos |
| **Tarefa agendada** | Criar tarefas <br> Modificar tarefa <br> Remover tarefa <br> Executar tarefa |
| **Das** | Representar usuário <br> Mensagem <br> Validar sessão <br> Exibir e parar sessões |
| **Cluster de repositório de armazenamento** | Configurar um cluster de repositório de armazenamento |
| **Armazenamento controlado por perfil** | Atualização de armazenamento controlada por perfil <br> Exibição de armazenamento controlada por perfil |
| **Exibições de armazenamento** | Configurar serviço <br> Vista |
| **Tarefas** | Criar tarefa <br> Atualizar tarefa |
| **Serviço de transferência**| Gerir <br> Monitorizar |
| **vApp** | Adicionar máquina virtual <br> Atribuir pool de recursos <br> Atribuir vApp <br> Clone <br> Create <br> Eliminar <br> Exportar <br> Importar <br> Mover <br> Desligar <br> Ligar <br> Mudar o nome <br> Suspender <br> Anular o Registo <br> Exibir ambiente OVF <br> configuração do aplicativo vApp <br> configuração da instância de vApp <br> configuração do vApp managedBy <br> configuração do recurso vApp |
| **VRMPolicy** | VRMPolicy de consulta <br> Atualizar VRMPolicy |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicionar novo disco <br> Adicionar ou remover dispositivo <br> Segurança <br> Alterar contagem de CPU <br> Alterar recurso <br> Configurar o managedBy <br> Controle de alterações do disco <br> Concessão de disco <br> Exibir configurações de conexão <br> Estender disco virtual <br> Dispositivo USB do host <br> Memória <br> Modificar configurações do dispositivo <br> Compatibilidade de tolerância a falhas de consulta <br> Consultar arquivos sem proprietário <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover disco <br> Mudar o nome <br> Redefinir informações de convidado <br> Definir anotação <br> Definições <br> Posicionamento do Swapfile <br> Alternar bifurcação pai <br> Desbloquear máquina virtual <br> Atualizar a compatibilidade da máquina virtual |
| **Operações de convidado de > de máquina virtual** | Modificação do alias da operação de convidado <br> Consulta de alias de operação de convidado <br> Modificações de operação de convidado <br> Execução do programa de operação de convidado <br> Consultas de operação de convidado |
| **Interação de > de máquina virtual** | Pergunta de resposta <br> Operação de backup na máquina virtual <br> Configurar mídia de CD <br> Configurar mídia de disquete <br> Interação do console <br> Criar captura de tela <br> Desfragmentar todos os discos <br> Conexão do dispositivo <br> Arrastar e soltar <br> Gerenciamento do sistema operacional convidado pela API do VIX <br> Injetar códigos de verificação de HID USB <br> Pausar ou pausar <br> Executar operações de apagamento ou redução <br> Desligar <br> Ligar <br> Registrar sessão na máquina virtual <br> Repetir sessão na máquina virtual <br> Repor <br> Retomar tolerância a falhas <br> Suspender <br> Tolerância a falhas de suspensão <br> Ativação pós-falha de teste <br> VM secundária de reinício de teste <br> Desativar tolerância a falhas <br> Ativar tolerância a falhas <br> Instalação das ferramentas do VMware |
| **Inventário de > de máquina virtual** | Criar a partir de existente <br> Criar novo <br> Mover <br> Registar <br> Remover <br> Anular o Registo |
| **Provisionamento de > de máquina virtual** | Permitir acesso ao disco <br> Permitir acesso a arquivos <br> Permitir acesso ao disco somente leitura <br> Permitir download de máquina virtual <br> Permitir carregamento de arquivos de máquina virtual <br> Clonar modelo <br> Clonar máquina virtual <br> Criar modelo a partir da máquina virtual <br> Personalizar <br> Implementar o modelo <br> Marcar como modelo <br> Marcar como máquina virtual <br> Modificar especificação de personalização <br> Promover discos <br> Ler especificações de personalização |
| **Configuração do serviço de > de máquina virtual** | Permitir notificações <br> Permitir sondagem de notificações de eventos globais <br> Gerenciar configurações de serviço <br> Modificar configuração de serviço <br> Configurações do serviço de consulta <br> Ler configuração de serviço |
| **Gerenciamento de instantâneos de > de máquina virtual** | Criar instantâneo <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação vSphere de máquina virtual >** | Configurar a replicação <br> Gerir a replicação <br> Replicação do monitor |
| **vService** | Criar dependência <br> Destruir dependência <br> Reconfigurar a configuração de dependência <br> Atualizar dependência |

### <a name="cloud-cluster-admin-role"></a>Cloud-cluster-admin-role

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Repositório** | Alocar espaço <br> Procurar repositório de armazenamento <br> Configurar repositório de armazenamento <br> Operações de arquivo de nível baixo <br> Remover repositório de armazenamento <br> Renomear repositório de armazenamento <br> Atualizar arquivos de máquina virtual <br> Atualizar metadados da máquina virtual |
| **Pasta** | Criar pasta <br> Excluir pasta <br> Mover pasta <br> Renomear pasta |
| **Configuração de > de host**  | Configuração da partição de armazenamento |
| **Marcação de vSphere** | Atribuir ou cancelar a atribuição de marca vSphere <br> Criar marca vSphere <br> Criar categoria de marca vSphere <br> Excluir marca vSphere <br> Excluir categoria de marca vSphere <br> Editar marca vSphere <br> Editar categoria da marca vSphere <br> Modificar o campo UsedBy para a categoria <br> Modificar campo UsedBy para marca |
| **Rede** | Atribuir rede |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao pool de recursos <br> Atribuir máquina virtual ao pool de recursos <br> Criar pool de recursos <br> Migrar máquina virtual desligada <br> Migrar máquina virtual ligada <br> Modificar pool de recursos <br> Mover pool de recursos <br> Consultar o vMotion <br> Remover pool de recursos <br> Renomear pool de recursos |
| **vApp** | Adicionar máquina virtual <br> Atribuir pool de recursos <br> Atribuir vApp <br> Clone <br> Create <br> Eliminar <br> Exportar <br> Importar <br> Mover <br> Desligar <br> Ligar <br> Mudar o nome <br> Suspender <br> Anular o Registo <br> Exibir ambiente OVF <br> configuração do aplicativo vApp <br> configuração da instância de vApp <br> configuração do vApp managedBy <br> configuração do recurso vApp |
| **VRMPolicy** | VRMPolicy de consulta <br> Atualizar VRMPolicy |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicionar novo disco <br> Adicionar ou remover dispositivo <br> Segurança <br> Alterar contagem de CPU <br> Alterar recurso <br> Configurar o managedBy <br> Controle de alterações do disco <br> Concessão de disco <br> Exibir configurações de conexão <br> Estender disco virtual <br> Dispositivo USB do host <br> Memória <br> Modificar configurações do dispositivo <br> Compatibilidade de tolerância a falhas de consulta <br> Consultar arquivos sem proprietário <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover disco <br> Mudar o nome <br> Redefinir informações de convidado <br> Definir anotação <br> Definições <br> Posicionamento do Swapfile <br> Alternar bifurcação pai <br> Desbloquear máquina virtual <br> Atualizar a compatibilidade da máquina virtual |
| **Operações de convidado de > de máquina virtual** | Modificação do alias da operação de convidado <br> Consulta de alias de operação de convidado <br> Modificações de operação de convidado <br> Execução do programa de operação de convidado <br> Consultas de operação de convidado |
| **Interação de > de máquina virtual** | Pergunta de resposta <br> Operação de backup na máquina virtual <br> Configurar mídia de CD <br> Configurar mídia de disquete <br> Interação do console <br> Criar captura de tela <br> Desfragmentar todos os discos <br> Conexão do dispositivo <br> Arrastar e soltar <br> Gerenciamento do sistema operacional convidado pela API do VIX <br> Injetar códigos de verificação de HID USB <br> Pausar ou pausar <br> Executar operações de apagamento ou redução <br> Desligar <br> Ligar <br> Registrar sessão na máquina virtual <br> Repetir sessão na máquina virtual <br> Repor <br> Retomar tolerância a falhas <br> Suspender <br> Tolerância a falhas de suspensão <br> Ativação pós-falha de teste <br> VM secundária de reinício de teste <br> Desativar tolerância a falhas <br> Ativar tolerância a falhas <br> Instalação das ferramentas do VMware
| **Inventário de > de máquina virtual** | Criar a partir de existente <br> Criar novo <br> Mover <br> Registar <br> Remover <br> Anular o Registo |
| **Provisionamento de > de máquina virtual** | Permitir acesso ao disco <br> Permitir acesso a arquivos <br> Permitir acesso ao disco somente leitura <br> Permitir download de máquina virtual <br> Permitir carregamento de arquivos de máquina virtual <br> Clonar modelo <br> Clonar máquina virtual <br> Criar modelo a partir da máquina virtual <br> Personalizar <br> Implementar o modelo <br> Marcar como modelo <br> Marcar como máquina virtual <br> Modificar especificação de personalização <br> Promover discos  <br> Ler especificações de personalização |
| **Configuração do serviço de > de máquina virtual** | Permitir notificações <br> Permitir sondagem de notificações de eventos globais <br> Gerenciar configurações de serviço <br> Modificar configuração de serviço <br> Configurações do serviço de consulta <br> Ler configuração de serviço
| **Gerenciamento de instantâneos de > de máquina virtual** | Criar instantâneo <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação vSphere de máquina virtual >** | Configurar a replicação <br> Gerir a replicação <br> Replicação do monitor |
| **vService** | Criar dependência <br> Destruir dependência <br> Reconfigurar a configuração de dependência <br> Atualizar dependência |

### <a name="cloud-storage-admin-role"></a>Cloud-Storage-admin-role

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Repositório** | Alocar espaço <br> Procurar repositório de armazenamento <br> Configurar repositório de armazenamento <br> Operações de arquivo de nível baixo <br> Remover repositório de armazenamento <br> Renomear repositório de armazenamento <br> Atualizar arquivos de máquina virtual <br> Atualizar metadados da máquina virtual |
| **Configuração de > de host** | Configuração da partição de armazenamento |
| **Cluster de repositório de armazenamento** | Configurar um cluster de repositório de armazenamento |
| **Armazenamento controlado por perfil** | Atualização de armazenamento controlada por perfil <br> Exibição de armazenamento controlada por perfil |
| **Exibições de armazenamento** | Configurar serviço <br> Vista |

### <a name="cloud-network-admin-role"></a>Cloud-Network-admin-role

| **Categoria** | **Privilégio** |
|----------|-----------|
| **grupo de dvPort** | Create <br> Eliminar <br> Modificar <br> Operação de política <br> Operação de escopo |
| **Rede** | Atribuir rede <br> Configurar <br> Mover rede <br> Remover |
| **Configuração de > de máquina virtual** | Modificar configurações do dispositivo |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-admin-role

| **Categoria** | **Privilégio** |
|----------|-----------|
| **Repositório** | Alocar espaço <br> Procurar repositório de armazenamento |
| **Rede** | Atribuir rede |
| **Recurso** | Atribuir máquina virtual ao pool de recursos <br> Migrar máquina virtual desligada <br> Migrar máquina virtual ligada
| **vApp** | Exportar <br> Importar |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicionar novo disco <br> Adicionar ou remover dispositivo <br> Segurança <br> Alterar contagem de CPU <br> Alterar recurso <br> Configurar o managedBy <br> Controle de alterações do disco <br> Concessão de disco <br> Exibir configurações de conexão <br> Estender disco virtual <br> Dispositivo USB do host <br> Memória <br> Modificar configurações do dispositivo <br> Compatibilidade de tolerância a falhas de consulta <br> Consultar arquivos sem proprietário <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover disco <br> Mudar o nome <br> Redefinir informações de convidado <br> Definir anotação <br> Definições <br> Posicionamento do Swapfile <br> Alternar bifurcação pai <br> Desbloquear máquina virtual <br> Atualizar a compatibilidade da máquina virtual |
| **Operações de convidado de > de máquina virtual** | Modificação do alias da operação de convidado <br> Consulta de alias de operação de convidado <br> Modificações de operação de convidado <br> Execução do programa de operação de convidado <br> Consultas de operação de convidado    |
| **Interação de > de máquina virtual** | Pergunta de resposta <br> Operação de backup na máquina virtual <br> Configurar mídia de CD <br> Configurar mídia de disquete <br> Interação do console <br> Criar captura de tela <br> Desfragmentar todos os discos <br> Conexão do dispositivo <br> Arrastar e soltar <br> Gerenciamento do sistema operacional convidado pela API do VIX <br> Injetar códigos de verificação de HID USB <br> Pausar ou pausar <br> Executar operações de apagamento ou redução <br> Desligar <br> Ligar <br> Registrar sessão na máquina virtual <br> Repetir sessão na máquina virtual <br> Repor <br> Retomar tolerância a falhas <br> Suspender <br> Tolerância a falhas de suspensão <br> Ativação pós-falha de teste <br> VM secundária de reinício de teste <br> Desativar tolerância a falhas <br> Ativar tolerância a falhas <br> Instalação das ferramentas do VMware |
| **Inventário de > de máquina virtual** | Criar a partir de existente <br> Criar novo <br> Mover <br> Registar <br> Remover <br> Anular o Registo |
| **Provisionamento de > de máquina virtual** | Permitir acesso ao disco <br> Permitir acesso a arquivos <br> Permitir acesso ao disco somente leitura <br> Permitir download de máquina virtual <br> Permitir carregamento de arquivos de máquina virtual <br> Clonar modelo <br> Clonar máquina virtual <br> Criar modelo a partir da máquina virtual <br> Personalizar <br> Implementar o modelo <br> Marcar como modelo <br> Marcar como máquina virtual <br> Modificar especificação de personalização <br> Promover discos <br> Ler especificações de personalização |
| **Configuração do serviço de > de máquina virtual** | Permitir notificações <br> Permitir sondagem de notificações de eventos globais <br> Gerenciar configurações de serviço <br> Modificar configuração de serviço <br> Configurações do serviço de consulta <br> Ler configuração de serviço
| **Gerenciamento de instantâneos de > de máquina virtual** | Criar instantâneo <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação vSphere de máquina virtual >** | Configurar a replicação <br> Gerir a replicação <br> Replicação do monitor |
| **vService** | Criar dependência <br> Destruir dependência <br> Reconfigurar a configuração de dependência <br> Atualizar dependência |
