---
title: Solução Azure VMware by CloudSimple - Modelo de permissão em nuvem privada
description: Descreve o modelo, grupos e categorias de permissão cloudsimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1c8cfeda008955006f2fbad1df58c8047bd36541
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898050"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>CloudSimple Modelo de permissão em nuvem privada de VMware vCenter

CloudSimple mantém acesso administrativo total ao ambiente Private Cloud. A cada cliente CloudSimple é-lhe concedido privilégios administrativos suficientes para poder implantar e gerir as máquinas virtuais no seu ambiente.  Se necessário, pode aumentar temporariamente os seus privilégios para desempenhar funções administrativas.

## <a name="cloud-owner"></a>Proprietário de nuvem

Quando cria uma Nuvem Privada, um utilizador **CloudOwner** é criado no domínio vCenter Single Sign-On, com acesso **cloud-Owner-Role** para gerir objetos na Nuvem Privada. Este utilizador também pode configurar fontes de [identidade vCenter](set-vcenter-identity.md)adicionais , e outros utilizadores para o Private Cloud vCenter.

> [!NOTE]
> O utilizador predefinido para o seu CloudSimple Private Cloud vCenter é cloudowner@cloudsimple.local quando uma Nuvem Privada é criada.

## <a name="user-groups"></a>Grupos de Utilizadores

Um grupo chamado **Cloud-Owner-Group** é criado durante a implantação de uma Nuvem Privada. Os utilizadores deste grupo podem administrar várias partes do ambiente vSphere na Nuvem Privada. Este grupo recebe automaticamente privilégios  **cloud-Owner-Role,** e o utilizador  **CloudOwner** é adicionado como membro deste grupo.  A CloudSimple cria grupos adicionais com privilégios limitados para facilitar a gestão.  Pode adicionar qualquer utilizador a estes grupos pré-criados e os privilégios abaixo definidos são automaticamente atribuídos aos utilizadores dos grupos.

### <a name="pre-created-groups"></a>Grupos pré-criados

| Nome do Grupo | Objetivo | Função |
| -------- | ------- | ------ |
| Cloud-Owner-Group | Membros deste grupo têm privilégios administrativos para o Private Cloud vCenter | [Cloud-Owner-Role](#cloud-owner-role) |
| Cloud-Global-Cluster-Admin-Group | Membros deste grupo têm privilégios administrativos no Cluster Private Cloud vCenter | [Papel de administrador-aglomerado de nuvem](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-Admin-Group | Membros deste grupo podem gerir o armazenamento no Private Cloud vCenter | [Papel de administrador de armazenamento em nuvem](#cloud-storage-admin-role) |
| Cloud-Global-Network-Admin-Group | Os membros deste grupo podem gerir grupos portais de rede e distribuídos no Private Cloud vCenter | [Papel de administrador em rede de nuvem](#cloud-network-admin-role) |
| Cloud-Global-VM-Admin-Group | Membros deste grupo podem gerir máquinas virtuais no private Cloud vCenter | [Cloud-VM-Admin-Role](#cloud-vm-admin-role) |

Para conceder permissão a cada utilizador para gerir a Nuvem Privada, crie contas de utilizador adicionam aos grupos apropriados.

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores *adicionados* ao grupo de Administradores serão removidos automaticamente.  Apenas as contas de serviço devem ser *adicionadas* ao grupo de administradores e as contas de serviço não devem ser usadas para iniciar súm na uI web vSphere.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lista de privilégios vCenter para funções predefinidos

### <a name="cloud-owner-role"></a>Cloud-Owner-Role

| **Categoria** | **Privilege** |
|----------|-----------|
| **Alarmes** | Alarme de reconhecimento <br> Criar alarme <br> Desativar a ação de alarme <br> Modificar o alarme <br> Remover o alarme <br> Definir estado de alarme |
| **Permissões** | Modificar a permissão |
| **Biblioteca de Conteúdos** | Adicionar item da biblioteca <br> Criar biblioteca local <br> Criar biblioteca subscrita <br> Apagar item da biblioteca <br> Apagar biblioteca local <br> Excluir biblioteca subscrita <br> Transferir ficheiros <br> Artigo da biblioteca de despejo <br> Despejar biblioteca subscrita <br> Armazenamento de importação <br> Informações de subscrição da sonda <br> Ler armazenamento <br> Artigo da biblioteca sincronizada <br> Biblioteca sincronizada <br> Introspeção tipo <br> Atualizar definições de configuração <br> Atualizar ficheiros <br> Atualizar biblioteca <br> Atualizar item da biblioteca <br> Atualizar biblioteca local <br> Atualização da biblioteca subscrita <br> Ver definições de configuração |
| **Operações criptográficas** | Adicionar disco <br> Clone <br> Desencriptar <br> Acesso Direto <br> Encriptar <br> Criptografe novo <br> Gerir kms <br> Gerir políticas de encriptação <br> Gerir chaves <br> Migrar <br> Recripto <br> Registar VM <br> Anfitrião de registo |
| **grupo dvPort** | Criar <br> Eliminar <br> Modificar <br> Operação política <br> Operação de âmbito |
| **Arquivo de dados** | Alocar espaço <br> Procurar loja de dados <br> Configure datastore <br> Operações de ficheiro de baixo nível <br> Mover datastore <br> Remover datastore <br> Remover ficheiro <br> Renomear datastore <br> Atualizar ficheiros de máquinas virtuais <br> Atualizar metadados de máquinas virtuais |
| **Gestor de agente da ESX** | Configurar <br> Modificar <br> Vista |
| **Extensão** | Extensão de registo <br> Extensão do não registo <br> Extensão de atualização |
| **Fornecedor de estatísticas externas**| Registar <br> Anular o Registo <br> Atualizar |
| **Pasta** | Criar pasta <br> Eliminar pasta <br> Mover pasta <br> Pasta de renome |
| **Global** | Cancelar tarefa <br> Planeamento de capacidade <br> Diagnóstico <br> Métodos de desativação <br> Permitir métodos <br> Etiqueta global <br> Saúde <br> Licenças <br> Evento de registo <br> Gerir atributos personalizados <br> Proxy <br> Ação do script <br> Gestores de serviços <br> Definir atributo personalizado <br> Etiqueta de sistema |
| **Prestador de atualização de saúde** | Registar <br> Anular o Registo <br> Atualizar |
| **Configuração > do anfitrião** | Configuração de partição de armazenamento |
| **Inventário de > anfitrião** | Modificar o cluster |
| **vSphere Tagging** | Atribuir ou Atribuir vSphere Tag <br> Criar vSphere Tag <br> Criar categoria vSphere Tag <br> Eliminar vSphere Tag <br> Eliminar categoria de tags vSphere <br> Editar vSphere Tag <br> Editar vSphere Tag Category <br> Modificar o campo usado por categoria <br> Modificar o campo usado por etiqueta |
| **Rede** | Atribuir rede <br> Configurar <br> Rede de movimentos <br> Remover |
| **Desempenho** | Modificar intervalos |
| **Perfil do anfitrião** | Vista |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao pool de recursos <br> Atribuir máquina virtual ao pool de recursos <br> Criar piscina de recursos <br> Migrar alimentado fora da máquina virtual <br> Migrar alimentado em máquina virtual <br> Modificar o conjunto de recursos <br> Mover piscina de recursos <br> Consulta vMotion <br> Remover piscina de recursos <br> Rebatize piscina de recursos |
| **Tarefa agendada** | Criar tarefas <br> Modificar tarefa <br> Remover tarefa <br> Executar tarefa |
| **Sessões** | Personificar utilizador <br> Mensagem <br> Validar sessão <br> Ver e parar sessões |
| **Cluster datastore** | Configurar um cluster de datastore |
| **Armazenamento orientado para o perfil** | Atualização de armazenamento orientada para o perfil <br> Vista de armazenamento orientada para o perfil |
| **Vistas de armazenamento** | Serviço de configuração <br> Vista |
| **Tasks** | Criar tarefa <br> Atualizar tarefa |
| **Serviço de transfer**| Gerir <br> Monitorizar |
| **vApp** | Adicionar máquina virtual <br> Atribuir piscina de recursos <br> Atribuir vApp <br> Clone <br> Criar <br> Eliminar <br> Exportar <br> Importar <br> Mover <br> Desligar <br> Ligado <br> Mudar o Nome <br> Suspender <br> Anular o Registo <br> Ver ambiente OVF <br> configuração de aplicação vApp <br> configuração de instância vApp <br> vApp gerido Por configuração <br> configuração de recursos vApp |
| **VRMPolicy** | Consulta VRMPolicy <br> Atualizar VRMPolicy |
| **Configuração de > de máquinas virtuais** | Adicionar disco existente <br> Adicionar disco novo <br> Adicionar ou remover dispositivo <br> Avançado <br> Alterar contagem de CPU <br> Alterar recurso <br> Configurar gerido Por <br> Rastreio de mudança de disco <br> Arrendamento em disco <br> Definições de ligação de exibição <br> Estender o disco virtual <br> Dispositivo USB anfitrião <br> Memória <br> Modificar as definições do dispositivo <br> Consulta compatibilidade com a tolerância à falha <br> Consulta de ficheiros não desnudos <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover o disco <br> Mudar o Nome <br> Redefinir informações dos hóspedes <br> Definir anotação <br> Definições <br> Colocação de ficheiros de swap <br> Toggle fork parent <br> Desbloqueie a máquina virtual <br> Atualizar compatibilidade da máquina virtual |
| **Máquina virtual > operações de hóspedes** | Modificação do pseudónimo da operação de hóspedes <br> Consulta de pseudónimos de operação de hóspedes <br> Modificações da operação do hóspede <br> Execução do programa de operação de hóspedes <br> Consultas de operação de hóspedes |
| **Interação de > de máquinas virtuais** | Pergunta de resposta <br> Operação de backup na máquina virtual <br> Configure os meios de comunicação de CD <br> Configurar os meios de comunicação floppy <br> Interação da consola <br> Criar screenshot <br> Desfragmentação de todos os discos <br> Ligação do dispositivo <br> Arrastar e largar <br> Gestão do sistema operativo convidado pela VIX API <br> Injetar códigos de verificação USB HID <br> Pausa ou Unpause <br> Realizar operações de limpeza ou de redução <br> Desligar <br> Ligado <br> Sessão de gravação na máquina virtual <br> Sessão de repetição na máquina virtual <br> Repor <br> Retomar a tolerância à falha <br> Suspender <br> Suspender a tolerância à falha <br> Ativação pós-falha de teste <br> Teste reiniciar VM secundário <br> Desligue a tolerância à falha <br> Ligue a tolerância à falha <br> Instalação de Ferramentas VMware |
| **Inventário de > de máquinas virtuais** | Criar a partir de existente <br> Criar novo <br> Mover <br> Registar <br> Remover <br> Anular o Registo |
| **Provisão de > de máquinas virtuais** | Permitir o acesso ao disco <br> Permitir o acesso a ficheiros <br> Permitir o acesso ao disco apenas de leitura <br> Permitir o download de máquinas virtuais <br> Permitir o upload de ficheiros de máquinas virtuais <br> Modelo de clone <br> Máquina virtual clone <br> Criar modelo a partir de máquina virtual <br> Personalizar <br> Implementar o modelo <br> Marque como modelo <br> Marque como máquina virtual <br> Modifique a especificação de personalização <br> Promover discos <br> Leia as especificações de personalização |
| **Configuração de serviço de > de máquina virtual** | Permitir notificações <br> Permitir sondagens de notificações globais de eventos <br> Gerir configurações de serviço <br> Modificar a configuração do serviço <br> Configurações de serviço de consulta <br> Ler configuração de serviço |
| **Gestão de > snapshot de máquina virtual** | Criar instantâneo <br> Remover instantâneo <br> Instantâneo de renome <br> Reverter para instantâneo |
| **Réplica de > de máquinas virtuais vSphere** | Configurar a replicação <br> Gerir a replicação <br> Monitorizar a replicação |
| **vServiço** | Criar dependência <br> Destruir a dependência <br> Reconfigurar a configuração da dependência <br> Atualizar a dependência |

### <a name="cloud-cluster-admin-role"></a>Papel de administrador-aglomerado de nuvem

| **Categoria** | **Privilege** |
|----------|-----------|
| **Arquivo de dados** | Alocar espaço <br> Procurar loja de dados <br> Configure datastore <br> Operações de ficheiro de baixo nível <br> Remover datastore <br> Renomear datastore <br> Atualizar ficheiros de máquinas virtuais <br> Atualizar metadados de máquinas virtuais |
| **Pasta** | Criar pasta <br> Eliminar pasta <br> Mover pasta <br> Pasta de renome |
| **Configuração > do anfitrião**  | Configuração de partição de armazenamento |
| **vSphere Tagging** | Atribuir ou Atribuir vSphere Tag <br> Criar vSphere Tag <br> Criar categoria vSphere Tag <br> Eliminar vSphere Tag <br> Eliminar categoria de tags vSphere <br> Editar vSphere Tag <br> Editar vSphere Tag Category <br> Modificar o campo usado por categoria <br> Modificar o campo usado por etiqueta |
| **Rede** | Atribuir rede |
| **Recurso** | Aplicar recomendação <br> Atribuir vApp ao pool de recursos <br> Atribuir máquina virtual ao pool de recursos <br> Criar piscina de recursos <br> Migrar alimentado fora da máquina virtual <br> Migrar alimentado em máquina virtual <br> Modificar o conjunto de recursos <br> Mover piscina de recursos <br> Consulta vMotion <br> Remover piscina de recursos <br> Rebatize piscina de recursos |
| **vApp** | Adicionar máquina virtual <br> Atribuir piscina de recursos <br> Atribuir vApp <br> Clone <br> Criar <br> Eliminar <br> Exportar <br> Importar <br> Mover <br> Desligar <br> Ligado <br> Mudar o Nome <br> Suspender <br> Anular o Registo <br> Ver ambiente OVF <br> configuração de aplicação vApp <br> configuração de instância vApp <br> vApp gerido Por configuração <br> configuração de recursos vApp |
| **VRMPolicy** | Consulta VRMPolicy <br> Atualizar VRMPolicy |
| **Configuração de > de máquinas virtuais** | Adicionar disco existente <br> Adicionar disco novo <br> Adicionar ou remover dispositivo <br> Avançado <br> Alterar contagem de CPU <br> Alterar recurso <br> Configurar gerido Por <br> Rastreio de mudança de disco <br> Arrendamento em disco <br> Definições de ligação de exibição <br> Estender o disco virtual <br> Dispositivo USB anfitrião <br> Memória <br> Modificar as definições do dispositivo <br> Consulta compatibilidade com a tolerância à falha <br> Consulta de ficheiros não desnudos <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover o disco <br> Mudar o Nome <br> Redefinir informações dos hóspedes <br> Definir anotação <br> Definições <br> Colocação de ficheiros de swap <br> Toggle fork parent <br> Desbloqueie a máquina virtual <br> Atualizar compatibilidade da máquina virtual |
| **Máquina virtual > operações de hóspedes** | Modificação do pseudónimo da operação de hóspedes <br> Consulta de pseudónimos de operação de hóspedes <br> Modificações da operação do hóspede <br> Execução do programa de operação de hóspedes <br> Consultas de operação de hóspedes |
| **Interação de > de máquinas virtuais** | Pergunta de resposta <br> Operação de backup na máquina virtual <br> Configure os meios de comunicação de CD <br> Configurar os meios de comunicação floppy <br> Interação da consola <br> Criar screenshot <br> Desfragmentação de todos os discos <br> Ligação do dispositivo <br> Arrastar e largar <br> Gestão do sistema operativo convidado pela VIX API <br> Injetar códigos de verificação USB HID <br> Pausa ou Unpause <br> Realizar operações de limpeza ou de redução <br> Desligar <br> Ligado <br> Sessão de gravação na máquina virtual <br> Sessão de repetição na máquina virtual <br> Repor <br> Retomar a tolerância à falha <br> Suspender <br> Suspender a tolerância à falha <br> Ativação pós-falha de teste <br> Teste reiniciar VM secundário <br> Desligue a tolerância à falha <br> Ligue a tolerância à falha <br> Instalação de Ferramentas VMware
| **Inventário de > de máquinas virtuais** | Criar a partir de existente <br> Criar novo <br> Mover <br> Registar <br> Remover <br> Anular o Registo |
| **Provisão de > de máquinas virtuais** | Permitir o acesso ao disco <br> Permitir o acesso a ficheiros <br> Permitir o acesso ao disco apenas de leitura <br> Permitir o download de máquinas virtuais <br> Permitir o upload de ficheiros de máquinas virtuais <br> Modelo de clone <br> Máquina virtual clone <br> Criar modelo a partir de máquina virtual <br> Personalizar <br> Implementar o modelo <br> Marque como modelo <br> Marque como máquina virtual <br> Modifique a especificação de personalização <br> Promover discos  <br> Leia as especificações de personalização |
| **Configuração de serviço de > de máquina virtual** | Permitir notificações <br> Permitir sondagens de notificações globais de eventos <br> Gerir configurações de serviço <br> Modificar a configuração do serviço <br> Configurações de serviço de consulta <br> Ler configuração de serviço
| **Gestão de > snapshot de máquina virtual** | Criar instantâneo <br> Remover instantâneo <br> Instantâneo de renome <br> Reverter para instantâneo |
| **Réplica de > de máquinas virtuais vSphere** | Configurar a replicação <br> Gerir a replicação <br> Monitorizar a replicação |
| **vServiço** | Criar dependência <br> Destruir a dependência <br> Reconfigurar a configuração da dependência <br> Atualizar a dependência |

### <a name="cloud-storage-admin-role"></a>Papel de administrador de armazenamento em nuvem

| **Categoria** | **Privilege** |
|----------|-----------|
| **Arquivo de dados** | Alocar espaço <br> Procurar loja de dados <br> Configure datastore <br> Operações de ficheiro de baixo nível <br> Remover datastore <br> Renomear datastore <br> Atualizar ficheiros de máquinas virtuais <br> Atualizar metadados de máquinas virtuais |
| **Configuração > do anfitrião** | Configuração de partição de armazenamento |
| **Cluster datastore** | Configurar um cluster de datastore |
| **Armazenamento orientado para o perfil** | Atualização de armazenamento orientada para o perfil <br> Vista de armazenamento orientada para o perfil |
| **Vistas de armazenamento** | Serviço de configuração <br> Vista |

### <a name="cloud-network-admin-role"></a>Papel de administrador em rede de nuvem

| **Categoria** | **Privilege** |
|----------|-----------|
| **grupo dvPort** | Criar <br> Eliminar <br> Modificar <br> Operação política <br> Operação de âmbito |
| **Rede** | Atribuir rede <br> Configurar <br> Rede de movimentos <br> Remover |
| **Configuração de > de máquinas virtuais** | Modificar as definições do dispositivo |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-Admin-Role

| **Categoria** | **Privilege** |
|----------|-----------|
| **Arquivo de dados** | Alocar espaço <br> Procurar loja de dados |
| **Rede** | Atribuir rede |
| **Recurso** | Atribuir máquina virtual ao pool de recursos <br> Migrar alimentado fora da máquina virtual <br> Migrar alimentado em máquina virtual
| **vApp** | Exportar <br> Importar |
| **Configuração de > de máquinas virtuais** | Adicionar disco existente <br> Adicionar disco novo <br> Adicionar ou remover dispositivo <br> Avançado <br> Alterar contagem de CPU <br> Alterar recurso <br> Configurar gerido Por <br> Rastreio de mudança de disco <br> Arrendamento em disco <br> Definições de ligação de exibição <br> Estender o disco virtual <br> Dispositivo USB anfitrião <br> Memória <br> Modificar as definições do dispositivo <br> Consulta compatibilidade com a tolerância à falha <br> Consulta de ficheiros não desnudos <br> Dispositivo bruto <br> Recarregar do caminho <br> Remover o disco <br> Mudar o Nome <br> Redefinir informações dos hóspedes <br> Definir anotação <br> Definições <br> Colocação de ficheiros de swap <br> Toggle fork parent <br> Desbloqueie a máquina virtual <br> Atualizar compatibilidade da máquina virtual |
| **Máquina virtual >operações de hóspedes** | Modificação do pseudónimo da operação de hóspedes <br> Consulta de pseudónimos de operação de hóspedes <br> Modificações da operação do hóspede <br> Execução do programa de operação de hóspedes <br> Consultas de operação de hóspedes    |
| **Interação de >de máquinas virtuais** | Pergunta de resposta <br> Operação de backup na máquina virtual <br> Configure os meios de comunicação de CD <br> Configurar os meios de comunicação floppy <br> Interação da consola <br> Criar screenshot <br> Desfragmentação de todos os discos <br> Ligação do dispositivo <br> Arrastar e largar <br> Gestão do sistema operativo convidado pela VIX API <br> Injetar códigos de verificação USB HID <br> Pausa ou Unpause <br> Realizar operações de limpeza ou de redução <br> Desligar <br> Ligado <br> Sessão de gravação na máquina virtual <br> Sessão de repetição na máquina virtual <br> Repor <br> Retomar a tolerância à falha <br> Suspender <br> Suspender a tolerância à falha <br> Ativação pós-falha de teste <br> Teste reiniciar VM secundário <br> Desligue a tolerância à falha <br> Ligue a tolerância à falha <br> Instalação de Ferramentas VMware |
| **Inventário de >de máquinas virtuais** | Criar a partir de existente <br> Criar novo <br> Mover <br> Registar <br> Remover <br> Anular o Registo |
| **Provisão de >de máquinas virtuais** | Permitir o acesso ao disco <br> Permitir o acesso a ficheiros <br> Permitir o acesso ao disco apenas de leitura <br> Permitir o download de máquinas virtuais <br> Permitir o upload de ficheiros de máquinas virtuais <br> Modelo de clone <br> Máquina virtual clone <br> Criar modelo a partir de máquina virtual <br> Personalizar <br> Implementar o modelo <br> Marque como modelo <br> Marque como máquina virtual <br> Modifique a especificação de personalização <br> Promover discos <br> Leia as especificações de personalização |
| **Configuração de serviço de >de máquina virtual** | Permitir notificações <br> Permitir sondagens de notificações globais de eventos <br> Gerir configurações de serviço <br> Modificar a configuração do serviço <br> Configurações de serviço de consulta <br> Ler configuração de serviço
| **Gestão de >snapshot de máquina virtual** | Criar instantâneo <br> Remover instantâneo <br> Instantâneo de renome <br> Reverter para instantâneo |
| **Réplica de >de máquinas virtuais vSphere** | Configurar a replicação <br> Gerir a replicação <br> Monitorizar a replicação |
| **vServiço** | Criar dependência <br> Destruir a dependência <br> Reconfigurar a configuração da dependência <br> Atualizar a dependência |
