# 🔐 Objetos do Azure Key Vault: Guia Completo

No **Azure Key Vault**, a aba ou menu **Objetos (Objects)** reúne os três ativos fundamentais de segurança e criptografia gerenciados pelo serviço: **Keys (Chaves)**, **Secrets (Segredos)** e **Certificates (Certificados)**.

Embora todos sirvam para proteger informações sensíveis, cada objeto possui um propósito de arquitetura, ciclo de vida e mecanismo de processamento completamente distintos.

---

## 📐 Visão Geral da Aba "Objetos"

┌── 1. Keys (Chaves Criptográficas)
                          │    └─ Processamento ocorre DENTRO do Key Vault (HSM/Software).
                          │

Objetos no Azure Key Vault ───┼── 2. Secrets (Segredos)
│    └─ Armazenamento e entrega de strings de até 25 KB.
│
└── 3. Certificates (Certificados)
└─ Gestão automatizada do ciclo de vida de certificados X.509.

---

## 🔑 1. Keys (Chaves Criptográficas)

As **Keys** representam chaves criptográficas (assimétricas ou simétricas) mantidas dentro do Key Vault.

### ⚙️ Como funciona
Ao contrário dos *Secrets* (onde você baixa o valor para usar na sua aplicação), com as **Keys**, a chave privada **nunca deixa as fronteiras do Key Vault ou do HSM (Hardware Security Module)**. As operações criptográficas de assinatura ou descriptografia ocorrem *dentro* da infraestrutura do Key Vault.

### 🔹 Tipos de Chaves Suportadas
* **RSA / RSA-HSM:** Chaves de par público/privado (tamanhos de 2048, 3072 ou 4096 bits).
* **EC / EC-HSM (Elliptic Curve):** Criptografia de curva elíptica (P-256, P-384, P-521, SECP256K1).
* **oct-HSM:** Chaves simétricas mantidas exclusivamente em HSM para operações de alta velocidade.

### 🛠️ Operações Permitidas
* **Encrypt / Decrypt:** Criptografar e descriptografar blocos de dados.
* **Sign / Verify:** Assinar digerações (*hashes*) e verificar assinaturas.
* **Wrap Key / Unwrap Key:** Criptografar e descriptografar outras chaves de criptografia de dados (*Envelope Encryption*).

### 💡 Casos de Uso
* Criptografia de discos em máquinas virtuais (**Azure Disk Encryption**).
* Criptografia transparente de bancos de dados (**SQL Server TDE com BYOK - Bring Your Own Key**).
* Assinatura digital de tokens de autenticação sem expor a chave privada.

---

## 🔒 2. Secrets (Segredos)

Os **Secrets** são sequências de bytes ou texto simples (de até 25 KB) armazenados de forma criptografada em repouso.

### ⚙️ Como funciona
O Key Vault atua como um cofre centralizado de armazenamento e distribuição. A aplicação autenticada (via *Managed Identity* ou *Service Principal*) faz uma requisição e **baixa o valor bruto do segredo** para usá-lo na memória do processo.

### 🔹 Recursos Principais
* **Versionamento Automático:** Cada atualização de um segredo cria uma nova versão (mantendo as versões antigas disponíveis para consulta histórica ou *rollback*).
* **Data de Ativação / Expiração:** Permite definir `nbf` (*not before*) e `exp` (*expiration date*) para rotacionar credenciais com segurança.
* **Content Type (Opcional):** Campo livre para indicar o formato do dado armazenado (ex: `application/json`, `text/plain`, `connection-string`).

### 💡 Casos de Uso
* Strings de conexão com bancos de dados (PostgreSQL, SQL Server, MongoDB).
* Tokens de APIs de terceiros e chaves de acesso de contas de armazenamento (*Storage Account Keys*).
* Senhas de usuários de serviço e credenciais de bancos transacionais.
* Chaves privadas em formato `.pem` coladas como texto bruto.

---

## 📜 3. Certificates (Certificados)

Os **Certificates** fornecem suporte à gestão automatizada do ciclo de vida de certificados digitais **X.509**.

### ⚙️ Como funciona
Um objeto do tipo *Certificate* no Key Vault é uma abstração complexa composta por três partes interligadas:
1. Um **Certificate (Certificado X.509)** contendo os metadados e a chave pública.
2. Uma **Key (Chave)** correspondente à chave privada gerada.
3. Um **Secret (Segredo)** que armazena a chave e o certificado empacotados nos formatos `PFX` ou `PEM`.

### 🔹 Recursos de Automação
* **Renovação Automática:** O Key Vault pode renovar o certificado automaticamente antes do vencimento (ex: quando atingir 80% da vida útil).
* **Integração com CAs de Confiança:** Integração nativa com Autoridades Certificadoras parceiras (como DigiCert e GlobalSign) para emissão direta.
* **Geração de CSR (Certificate Signing Request):** Permite criar uma solicitação de assinatura para envio a CAs internas/privadas.

### 💡 Casos de Uso
* Certificados SSL/TLS para terminação HTTPS em **Azure Application Gateway** ou **Azure Front Door**.
* Autenticação de comunicação entre microsserviços (mTLS).
* Certificados para autenticação de aplicações no **Azure Entra ID (App Registrations)**.

---

## 📊 Tabela Comparativa: Keys vs. Secrets vs. Certificates

| Critério | 🔑 Keys (Chaves) | 🔒 Secrets (Segredos) | 📜 Certificates (Certificados) |
| :--- | :--- | :--- | :--- |
| **O que armazena?** | Chave criptográfica (RSA/EC). | Texto bruto (até 25 KB). | Certificado X.509 + Chave Privada/Pública. |
| **Onde roda a criptografia?** | **Dentro do Key Vault / HSM.** | Na aplicação do cliente. | Na aplicação ou em serviços Azure (App Gateway). |
| **Pode baixar a chave privada?** | ❌ Não (a chave privada não sai). | ✅ Sim (a aplicação lê o valor). | ✅ Sim (se permitido na política de criação). |
| **Possui Versionamento?** | ✅ Sim | ✅ Sim | ✅ Sim |
| **Gestão de Expiração Automática?** | ⚠️ Alertas via Event Grid. | ⚠️ Alertas via Event Grid. | ✅ Auto-renovação nativa com CAs. |
| **Melhor para...** | TDE de bancos, ADE de VMs e assinaturas. | Senhas, API Keys e Connection Strings. | SSL/TLS de sites, mTLS e HTTPS. |

---

## 🛡️ Recursos Globais de Segurança para Objetos

Independente do objeto criado (Key, Secret ou Certificate), o Azure Key Vault oferece mecanismos de governança em nível de cofre:

* **Soft Delete (Exclusão Suave):** Quando um objeto é excluído, ele entra em um estado de "lixeira" por um período configurável (de 7 a 90 dias), permitindo a recuperação em caso de exclusão acidental.
* **Purge Protection (Proteção contra Purga):** Impede que mesmo um administrador com privilégios máximos apague permanentemente o objeto antes do fim do período de *Soft Delete*, protegendo contra ataques de *ransomware*.
* **Azure RBAC vs. Access Policies:**
  * **RBAC (Recomendado):** Controle de acesso granular baseado em funções do Azure Entra ID (ex: *Key Vault Secrets User*, *Key Vault Crypto Officer*).
  * **Access Policies (Legado):** Permissões atribuídas por matriz de ações diretamente na lâmina do Key Vault.
