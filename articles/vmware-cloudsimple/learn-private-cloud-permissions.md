---
title: Solução Azure VMware by CloudSimple - Modelo de permissão de cloud privada
description: Descreve o modelo de permissão cloud privada Cloud Cloud, grupos e categorias
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 28c4dc7831f97d66eb4d47f08e640344d5cca0d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014951"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>CloudSimple Private Cloud modelo de permissão vCenter vCenter

A CloudSimple mantém o acesso administrativo total ao ambiente da Cloud Privada. A cada cliente CloudSimple é concedido privilégios administrativos suficientes para poder implementar e gerir as máquinas virtuais no seu ambiente.  Se necessário, pode aumentar temporariamente os seus privilégios para desempenhar funções administrativas.

## <a name="cloud-owner"></a>Proprietário da nuvem

Ao criar uma Cloud Privada, um utilizador **cloudOwner** é criado no domínio de sign-on single vCenter, com acesso **cloud-owner-role** para gerir objetos na Cloud Privada. Este utilizador também pode configurar fontes de [identidade vCenter](set-vcenter-identity.md)adicionais, e outros utilizadores para o vCenter private Cloud.

> [!NOTE]
> Utilizador padrão para o seu CloudSimple Private Cloud vCenter é cloudowner@cloudsimple.local quando uma Nuvem Privada é criada.

## <a name="user-groups"></a>Grupos de Utilizadores

Um grupo chamado **Cloud-Owner-Group** é criado durante a implantação de uma Nuvem Privada. Os utilizadores deste grupo podem administrar várias partes do ambiente vSphere na Nuvem Privada. Este grupo recebe automaticamente privilégios **cloud-owner-Role,** e o utilizador **CloudOwner** é adicionado como membro deste grupo.  A CloudSimple cria grupos adicionais com privilégios limitados para facilitar a gestão.  Pode adicionar qualquer utilizador a estes grupos pré-criados e os privilégios definidos a seguir são automaticamente atribuídos aos utilizadores dos grupos.

### <a name="pre-created-groups"></a>Grupos pré-criados

| Nome do Grupo | Objetivo | Função |
| -------- | ------- | ------ |
| Cloud-Owner-Group | Membros deste grupo têm privilégios administrativos para o Private Cloud vCenter | [Papel de proprietário de nuvem](#cloud-owner-role) |
| Cloud-Global-Cluster-Admin-Group | Membros deste grupo têm privilégios administrativos no Cluster VCenter Cloud Privada | [Cloud-Cluster-Admin-Role](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-Admin-Group | Membros deste grupo podem gerir o armazenamento no vCenter Private Cloud | [Papel de Cloud-Storage-Admin-Role](#cloud-storage-admin-role) |
| Cloud-Global-Network-Admin-Group | Os membros deste grupo podem gerir a rede e distribuir grupos portuários no vCenter private Cloud | [Cloud-Network-Admin-Role](#cloud-network-admin-role) |
| Cloud-Global-VM-Admin-Group | Membros deste grupo podem gerir máquinas virtuais no vCenter Private Cloud | [Cloud-VM-Admin-Role](#cloud-vm-admin-role) |

Para conceder permissões individuais aos utilizadores para gerir a Cloud Privada, crie contas de utilizador adicionando aos grupos apropriados.

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores adicionados ao grupo *Administradores* serão removidos automaticamente.  Apenas as contas de serviço devem ser adicionadas ao grupo *de administradores* e as contas de serviço não devem ser utilizadas para iniciar sessão na VSphere web UI.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lista de privilégios vCenter para funções padrão

### <a name="cloud-owner-role"></a>Papel de proprietário de nuvem

| **Categoria** | **Privilege** |
|----------|-----------|
| **Alarmes** | Alarme de reconhecimento <br> Criar alarme <br> Desativar a ação de alarme <br> Modificar o alarme <br> Remover o alarme <br> Definir estado de alarme |
| **Permissões** | Modificar a permissão |
| **Biblioteca de Conteúdos** | Adicionar item da biblioteca <br> Criar biblioteca local <br> Criar biblioteca subscrita <br> Eliminar artigo da biblioteca <br> Eliminar biblioteca local <br> Eliminar biblioteca subscrita <br> Transferir ficheiros <br> Despejo de artigo da biblioteca <br> Despejar biblioteca subscrita <br> Armazenamento de importação <br> Informações de subscrição de sonda <br> Ler armazenamento <br> Item da biblioteca sincronizada <br> Biblioteca sincronizada <br> Tipo de introspeção <br> Configurações de configuração de atualização <br> Atualizar ficheiros <br> Biblioteca atualizada <br> Atualizar item da biblioteca <br> Atualizar biblioteca local <br> Atualizar biblioteca subscrita <br> Ver definições de configuração |
| **Operações criptográficas** | Adicionar disco <br> Clone <br> Desencriptar <br> Acesso Direto <br> Encriptar <br> Criptografe novo <br> Gerir kms <br> Gerir políticas de encriptação <br> Gerir chaves <br> Migrar <br> Reencriptação <br> Registo VM <br> Registar anfitrião |
| **grupo dvPort** | Criar <br> Eliminar <br> Modificar <br> Operação política <br> Operação de âmbito de aplicação |
| **Arquivo de dados** | Alocar espaço <br> Navegue na loja de dados <br> Configure a loja de dados <br> Operações de ficheiros de baixo nível <br> Mover loja de dados <br> Remover a loja de dados <br> Remover ficheiro <br> Renomear a loja de dados <br> Atualizar ficheiros de máquinas virtuais <br> Atualizar metadados de máquinas virtuais |
| **Gestor de Agente ESX** | Configurar <br> Modificar <br> Vista |
| **Extensão** | Extensão de registo <br> Extensão de registo <br> Extensão de atualização |
| **Fornecedor de estatísticas externas**| Registar <br> Anular o Registo <br> Atualizar |
| **Pasta** | Criar pasta <br> Excluir pasta <br> Mover pasta <br> Pasta de renomear |
| **Global** | Cancelar tarefa <br> Planeamento de capacidade <br> Diagnóstico <br> Desativar métodos <br> Ativar métodos <br> Etiqueta global <br> Estado de Funcionamento <br> Licenças <br> Evento de log <br> Gerir atributos personalizados <br> Proxy <br> Ação do guião <br> Gestores de serviços <br> Definir atributo personalizado <br> Etiqueta do sistema |
| **Provedor de atualização de saúde** | Registar <br> Anular o Registo <br> Atualizar |
| **Configuração de > anfitrião** | Configuração da partilha de armazenamento |
| **Inventário de > anfitrião** | Modificar cluster |
| **marcação vSphere** | Atribuir ou Não Atribuir etiqueta vSphere <br> Criar tag vSphere <br> Criar a categoria de etiquetas vSphere <br> Eliminar etiqueta vSphere <br> Eliminar a categoria de etiquetas vSphere <br> Editar etiqueta vSphere <br> Editar categoria de etiqueta séris <br> Modificar campo usado para categoria <br> Modificar campo usado para etiqueta |
| **Rede** | Rede de atribuição <br> Configurar <br> Mover rede <br> Remover |
| **Desempenho** | Modificar intervalos |
| **Perfil de anfitrião** | Vista |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao conjunto de recursos <br> Atribuir máquina virtual ao conjunto de recursos <br> Criar um conjunto de recursos <br> Migrar alimentado da máquina virtual <br> Migrar alimentado em máquina virtual <br> Modificar o conjunto de recursos <br> Mover piscina de recursos <br> Consulta vMotion <br> Remover o conjunto de recursos <br> Renomear o conjunto de recursos |
| **Tarefa agendada** | Criar tarefas <br> Modificar tarefa <br> Remover tarefa <br> Executar tarefa |
| **Sessões** | Utilizador imitador <br> Mensagem <br> Sessão de validação <br> Ver e parar sessões |
| **Cluster de datastore** | Configure um cluster de datastore |
| **Armazenamento orientado por perfil** | Atualização de armazenamento orientada por perfil <br> Vista de armazenamento orientada por perfil |
| **Vistas de armazenamento** | Serviço de configuração <br> Vista |
| **Tasks** | Criar tarefa <br> Tarefa de atualização |
| **Serviço de transferência**| Gerir <br> Monitorizar |
| **vApp** | Adicionar máquina virtual <br> Atribuir conjunto de recursos <br> Atribuir vApp <br> Clone <br> Criar <br> Eliminar <br> Exportar <br> Importar <br> Mover <br> Desligar <br> Poder ligado <br> Mudar o Nome <br> Suspender <br> Anular o Registo <br> Ver ambiente OVF <br> configuração da aplicação vApp <br> configuração de instância vApp <br> vApp geridoPor configuração <br> configuração de recursos vApp |
| **VRMPolicy** | Consulta VRMPolicy <br> Atualizar a Política VRM |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicione novo disco <br> Adicionar ou remover dispositivo <br> Avançado <br> Alterar a contagem de CPU <br> Alterar recurso <br> Configure geridoBy <br> Rastreio de mudança de disco <br> Arrendamento de disco <br> Visualizar definições de ligação <br> Estender o disco virtual <br> Dispositivo USB anfitrião <br> Memória <br> Modificar as definições do dispositivo <br> Compatibilidade de tolerância à falha de consulta <br> Consulta de ficheiros não possuídos <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover o disco <br> Mudar o Nome <br> Redefinir as informações dos hóspedes <br> Definir anotação <br> Definições <br> Colocação de swapfile <br> Pai do garfo de alternância <br> Desbloquear máquina virtual <br> Atualizar compatibilidade com máquina virtual |
| **Máquina virtual > Operações de hóspedes** | Modificação do pseudónimo de operação convidada <br> Consulta de pseudónimode operação de hóspedes <br> Modificações de operação de hóspedes <br> Execução do programa de operação de hóspedes <br> Consultas de operação de hóspedes |
| **Interação > máquina virtual** | Resposta pergunta <br> Operação de backup na máquina virtual <br> Configure os meios de CD <br> Configure os meios de comunicação floppy <br> Interação de consolas <br> Criar screenshot <br> Desfragmentar todos os discos <br> Ligação do dispositivo <br> Arrastar e largar <br> Gestão do sistema operativo convidado pela VIX API <br> Injetar códigos de digitalização USB HID <br> Pausa ou Pausa <br> Realizar operações de limpeza ou de redução <br> Desligar <br> Poder ligado <br> Sessão de gravação na máquina virtual <br> Sessão de repetição na máquina virtual <br> Repor <br> Retomar a tolerância à falha <br> Suspender <br> Suspender a tolerância à falha <br> Ativação pós-falha de teste <br> Teste reiniciar VM secundário <br> Desligue a tolerância à falha <br> Ativar a tolerância à culpa <br> Instalação de ferramentas VMware |
| **Inventário de > de máquina virtual** | Criar a partir dos existentes <br> Criar novo <br> Mover <br> Registar <br> Remover <br> Anular o Registo |
| **Disposição > máquina virtual** | Permitir o acesso ao disco <br> Permitir o acesso ao ficheiro <br> Permitir o acesso ao disco apenas para leitura <br> Permitir o download de máquinas virtuais <br> Permitir o upload de ficheiros de máquinas virtuais <br> Modelo de clone <br> Máquina virtual de clonagem <br> Criar modelo a partir de máquina virtual <br> Personalizar <br> Implementar o modelo <br> Marque como modelo <br> Marque como máquina virtual <br> Modificar especificação de personalização <br> Promover discos <br> Ler especificações de personalização |
| **Configuração de serviço de > de máquina virtual** | Permitir notificações <br> Permitir a votação de notificações globais de eventos <br> Gerir configurações de serviço <br> Modificar a configuração do serviço <br> Configurações de serviço de consulta <br> Ler configuração do serviço |
| **Máquina virtual > gestão snapshot** | Criar instantâneo <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação de > vSphere** | Configurar a replicação <br> Gerir a replicação <br> Monitorizar a replicação |
| **vServiço** | Criar dependência <br> Destruir a dependência <br> Reconfigurar a configuração da dependência <br> Dependência de atualização |

### <a name="cloud-cluster-admin-role"></a>Cloud-Cluster-Admin-Role

| **Categoria** | **Privilege** |
|----------|-----------|
| **Arquivo de dados** | Alocar espaço <br> Navegue na loja de dados <br> Configure a loja de dados <br> Operações de ficheiros de baixo nível <br> Remover a loja de dados <br> Renomear a loja de dados <br> Atualizar ficheiros de máquinas virtuais <br> Atualizar metadados de máquinas virtuais |
| **Pasta** | Criar pasta <br> Excluir pasta <br> Mover pasta <br> Pasta de renomear |
| **Configuração de > anfitrião**  | Configuração da partilha de armazenamento |
| **marcação vSphere** | Atribuir ou Não Atribuir etiqueta vSphere <br> Criar tag vSphere <br> Criar a categoria de etiquetas vSphere <br> Eliminar etiqueta vSphere <br> Eliminar a categoria de etiquetas vSphere <br> Editar etiqueta vSphere <br> Editar categoria de etiqueta séris <br> Modificar campo usado para categoria <br> Modificar campo usado para etiqueta |
| **Rede** | Rede de atribuição |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao conjunto de recursos <br> Atribuir máquina virtual ao conjunto de recursos <br> Criar um conjunto de recursos <br> Migrar alimentado da máquina virtual <br> Migrar alimentado em máquina virtual <br> Modificar o conjunto de recursos <br> Mover piscina de recursos <br> Consulta vMotion <br> Remover o conjunto de recursos <br> Renomear o conjunto de recursos |
| **vApp** | Adicionar máquina virtual <br> Atribuir conjunto de recursos <br> Atribuir vApp <br> Clone <br> Criar <br> Eliminar <br> Exportar <br> Importar <br> Mover <br> Desligar <br> Poder ligado <br> Mudar o Nome <br> Suspender <br> Anular o Registo <br> Ver ambiente OVF <br> configuração da aplicação vApp <br> configuração de instância vApp <br> vApp geridoPor configuração <br> configuração de recursos vApp |
| **VRMPolicy** | Consulta VRMPolicy <br> Atualizar a Política VRM |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicione novo disco <br> Adicionar ou remover dispositivo <br> Avançado <br> Alterar a contagem de CPU <br> Alterar recurso <br> Configure geridoBy <br> Rastreio de mudança de disco <br> Arrendamento de disco <br> Visualizar definições de ligação <br> Estender o disco virtual <br> Dispositivo USB anfitrião <br> Memória <br> Modificar as definições do dispositivo <br> Compatibilidade de tolerância à falha de consulta <br> Consulta de ficheiros não possuídos <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover o disco <br> Mudar o Nome <br> Redefinir as informações dos hóspedes <br> Definir anotação <br> Definições <br> Colocação de swapfile <br> Pai do garfo de alternância <br> Desbloquear máquina virtual <br> Atualizar compatibilidade com máquina virtual |
| **Máquina virtual > Operações de hóspedes** | Modificação do pseudónimo de operação convidada <br> Consulta de pseudónimode operação de hóspedes <br> Modificações de operação de hóspedes <br> Execução do programa de operação de hóspedes <br> Consultas de operação de hóspedes |
| **Interação > máquina virtual** | Resposta pergunta <br> Operação de backup na máquina virtual <br> Configure os meios de CD <br> Configure os meios de comunicação floppy <br> Interação de consolas <br> Criar screenshot <br> Desfragmentar todos os discos <br> Ligação do dispositivo <br> Arrastar e largar <br> Gestão do sistema operativo convidado pela VIX API <br> Injetar códigos de digitalização USB HID <br> Pausa ou Pausa <br> Realizar operações de limpeza ou de redução <br> Desligar <br> Poder ligado <br> Sessão de gravação na máquina virtual <br> Sessão de repetição na máquina virtual <br> Repor <br> Retomar a tolerância à falha <br> Suspender <br> Suspender a tolerância à falha <br> Ativação pós-falha de teste <br> Teste reiniciar VM secundário <br> Desligue a tolerância à falha <br> Ativar a tolerância à culpa <br> Instalação de ferramentas VMware
| **Inventário de > de máquina virtual** | Criar a partir dos existentes <br> Criar novo <br> Mover <br> Registar <br> Remover <br> Anular o Registo |
| **Disposição > máquina virtual** | Permitir o acesso ao disco <br> Permitir o acesso ao ficheiro <br> Permitir o acesso ao disco apenas para leitura <br> Permitir o download de máquinas virtuais <br> Permitir o upload de ficheiros de máquinas virtuais <br> Modelo de clone <br> Máquina virtual de clonagem <br> Criar modelo a partir de máquina virtual <br> Personalizar <br> Implementar o modelo <br> Marque como modelo <br> Marque como máquina virtual <br> Modificar especificação de personalização <br> Promover discos  <br> Ler especificações de personalização |
| **Configuração de serviço de > de máquina virtual** | Permitir notificações <br> Permitir a votação de notificações globais de eventos <br> Gerir configurações de serviço <br> Modificar a configuração do serviço <br> Configurações de serviço de consulta <br> Ler configuração do serviço
| **Máquina virtual > gestão snapshot** | Criar instantâneo <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação de > vSphere** | Configurar a replicação <br> Gerir a replicação <br> Monitorizar a replicação |
| **vServiço** | Criar dependência <br> Destruir a dependência <br> Reconfigurar a configuração da dependência <br> Dependência de atualização |

### <a name="cloud-storage-admin-role"></a>Papel de Cloud-Storage-Admin-Role

| **Categoria** | **Privilege** |
|----------|-----------|
| **Arquivo de dados** | Alocar espaço <br> Navegue na loja de dados <br> Configure a loja de dados <br> Operações de ficheiros de baixo nível <br> Remover a loja de dados <br> Renomear a loja de dados <br> Atualizar ficheiros de máquinas virtuais <br> Atualizar metadados de máquinas virtuais |
| **Configuração de > anfitrião** | Configuração da partilha de armazenamento |
| **Cluster de datastore** | Configure um cluster de datastore |
| **Armazenamento orientado por perfil** | Atualização de armazenamento orientada por perfil <br> Vista de armazenamento orientada por perfil |
| **Vistas de armazenamento** | Serviço de configuração <br> Vista |

### <a name="cloud-network-admin-role"></a>Cloud-Network-Admin-Role

| **Categoria** | **Privilege** |
|----------|-----------|
| **grupo dvPort** | Criar <br> Eliminar <br> Modificar <br> Operação política <br> Operação de âmbito de aplicação |
| **Rede** | Rede de atribuição <br> Configurar <br> Mover rede <br> Remover |
| **Configuração de > de máquina virtual** | Modificar as definições do dispositivo |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-Admin-Role

| **Categoria** | **Privilege** |
|----------|-----------|
| **Arquivo de dados** | Alocar espaço <br> Navegue na loja de dados |
| **Rede** | Rede de atribuição |
| **Recurso** | Atribuir máquina virtual ao conjunto de recursos <br> Migrar alimentado da máquina virtual <br> Migrar alimentado em máquina virtual
| **vApp** | Exportar <br> Importar |
| **Configuração de > de máquina virtual** | Adicionar disco existente <br> Adicione novo disco <br> Adicionar ou remover dispositivo <br> Avançado <br> Alterar a contagem de CPU <br> Alterar recurso <br> Configure geridoBy <br> Rastreio de mudança de disco <br> Arrendamento de disco <br> Visualizar definições de ligação <br> Estender o disco virtual <br> Dispositivo USB anfitrião <br> Memória <br> Modificar as definições do dispositivo <br> Compatibilidade de tolerância à falha de consulta <br> Consulta de ficheiros não possuídos <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover o disco <br> Mudar o Nome <br> Redefinir as informações dos hóspedes <br> Definir anotação <br> Definições <br> Colocação de swapfile <br> Pai do garfo de alternância <br> Desbloquear máquina virtual <br> Atualizar compatibilidade com máquina virtual |
| **Máquina virtual >Operações de Hóspedes** | Modificação do pseudónimo de operação convidada <br> Consulta de pseudónimode operação de hóspedes <br> Modificações de operação de hóspedes <br> Execução do programa de operação de hóspedes <br> Consultas de operação de hóspedes    |
| **Interação >máquina virtual** | Resposta pergunta <br> Operação de backup na máquina virtual <br> Configure os meios de CD <br> Configure os meios de comunicação floppy <br> Interação de consolas <br> Criar screenshot <br> Desfragmentar todos os discos <br> Ligação do dispositivo <br> Arrastar e largar <br> Gestão do sistema operativo convidado pela VIX API <br> Injetar códigos de digitalização USB HID <br> Pausa ou Pausa <br> Realizar operações de limpeza ou de redução <br> Desligar <br> Poder ligado <br> Sessão de gravação na máquina virtual <br> Sessão de repetição na máquina virtual <br> Repor <br> Retomar a tolerância à falha <br> Suspender <br> Suspender a tolerância à falha <br> Ativação pós-falha de teste <br> Teste reiniciar VM secundário <br> Desligue a tolerância à falha <br> Ativar a tolerância à culpa <br> Instalação de ferramentas VMware |
| **Inventário de >de máquina virtual** | Criar a partir dos existentes <br> Criar novo <br> Mover <br> Registar <br> Remover <br> Anular o Registo |
| **Fornecimento de >de máquinavirtual** | Permitir o acesso ao disco <br> Permitir o acesso ao ficheiro <br> Permitir o acesso ao disco apenas para leitura <br> Permitir o download de máquinas virtuais <br> Permitir o upload de ficheiros de máquinas virtuais <br> Modelo de clone <br> Máquina virtual de clonagem <br> Criar modelo a partir de máquina virtual <br> Personalizar <br> Implementar o modelo <br> Marque como modelo <br> Marque como máquina virtual <br> Modificar especificação de personalização <br> Promover discos <br> Ler especificações de personalização |
| **Configuração do serviço de >de máquina virtual** | Permitir notificações <br> Permitir a votação de notificações globais de eventos <br> Gerir configurações de serviço <br> Modificar a configuração do serviço <br> Configurações de serviço de consulta <br> Ler configuração do serviço
| **Máquina virtual >gestão snapshot** | Criar instantâneo <br> Remover instantâneo <br> Renomear instantâneo <br> Reverter para instantâneo |
| **Replicação de >vSphere** | Configurar a replicação <br> Gerir a replicação <br> Monitorizar a replicação |
| **vServiço** | Criar dependência <br> Destruir a dependência <br> Reconfigurar a configuração da dependência <br> Dependência de atualização |
