# Lab 1: Banking Compliance Audit Simulation 🏦

## 🎯 Objective

Organize disorganized transaction logs into an ISO 27001-compliant structure under time pressure, simulating a real banking compliance audit scenario.

## 📋 Scenario Details

**Company:** International Bank (prod-bank-core-01 server)  
**Situation:** ACPR (French Banking Regulator) surprise audit announcement  
**Deadline:** 2 hours  
**Penalty:** €500,000 fine for non-compliance  
**Requirements:** ISO 27001 compliant log organization

## 🔧 Initial State
/var/log/transactions/
├── transaction_20231215.log
├── transaction_20240103.log
├── transaction_20240228.log
├── temp_cache_20230901.tmp
├── temp_cache_20240115.tmp
├── error_20231220.log
├── error_20240310.log
└── debug_session_20240201.tmp
**8 files** - disorganized, mixed dates, non-compliant temporary files present

## ✅ Requirements

1. **Create Compliant Structure:**
/opt/audit-compliance/
├── 2023/
│   ├── 12/
│   └── 09/
└── 2024/
├── 01/
├── 02/
└── 03/
2. **Migrate Files:** Move each .log file to appropriate YYYY/MM/ directory

3. **Create Backup:** Full recursive backup to `/backup/critical-logs/`

4. **Remove Non-Compliant Files:** Delete all .tmp files (GDPR violations)

5. **Generate Report:** Document:
   - Total .log files migrated
   - Total .tmp files deleted
   - Backup size and location

## 🛠️ Solution Approach

### Step 1: Setup Environment
```bash
# Create test environment
mkdir -p ~/bank-compliance-lab/var/log/transactions
cd ~/bank-compliance-lab/var/log/transactions

# Create test files
touch transaction_20231215.log transaction_20240103.log transaction_20240228.log
touch temp_cache_20230901.tmp temp_cache_20240115.tmp
touch error_20231220.log error_20240310.log
touch debug_session_20240201.tmp

# Add realistic content
echo "TRX-2023-12-15|AMOUNT:5000|STATUS:COMPLETED" > transaction_20231215.log
echo "TRX-2024-01-03|AMOUNT:12000|STATUS:PENDING" > transaction_20240103.log
echo "ERROR:TIMEOUT|2023-12-20|SERVER:prod-db-02" > error_20231220.log
```

### Step 2: Create Directory Structure
```bash
mkdir -p ~/opt/audit-compliance/2023/{09,12}
mkdir -p ~/opt/audit-compliance/2024/{01,02,03}

# Verify structure
tree ~/opt/audit-compliance/
```

### Step 3: Migrate Files by Date
```bash
# December 2023 files
mv transaction_20231215.log ~/opt/audit-compliance/2023/12/
mv error_20231220.log ~/opt/audit-compliance/2023/12/

# September 2023 files
mv temp_cache_20230901.tmp ~/opt/audit-compliance/2023/09/

# January 2024 files
mv transaction_20240103.log ~/opt/audit-compliance/2024/01/
mv temp_cache_20240115.tmp ~/opt/audit-compliance/2024/01/

# February 2024 files
mv transaction_20240228.log ~/opt/audit-compliance/2024/02/
mv debug_session_20240201.tmp ~/opt/audit-compliance/2024/02/

# March 2024 files
mv error_20240310.log ~/opt/audit-compliance/2024/03/

# Verify migration
tree ~/opt/audit-compliance/
ls -l ~/bank-compliance-lab/var/log/transactions/  # Should be empty
```

### Step 4: Create Backup
```bash
mkdir -p ~/backup/critical-logs/
cp -R ~/opt/audit-compliance/ ~/backup/critical-logs/

# Verify backup
tree ~/backup/critical-logs/
```

### Step 5: Remove Non-Compliant Files
```bash
# List .tmp files first (ALWAYS verify before deleting!)
ls ~/opt/audit-compliance/202*/??/*.tmp

# Delete with interactive confirmation
rm -i ~/opt/audit-compliance/202*/??/*.tmp

# Verify deletion
tree ~/opt/audit-compliance/  # Should show no .tmp files
```

### Step 6: Generate Audit Report
```bash
# Count migrated .log files
echo "Fichiers .log migrés: $(ls ~/opt/audit-compliance/202*/??/*.log | wc -l)"

# Document deleted .tmp files
echo "Fichiers .tmp supprimés: 3"

# Check backup size
echo "Taille backup: $(du -sh ~/backup/critical-logs/ | cut -f1)"

# Create formal report
cat > ~/rapport-audit.txt << EOF
==========================================
RAPPORT D'AUDIT - CONFORMITÉ BANCAIRE
Date: $(date)
==========================================

Fichiers .log migrés: $(ls ~/opt/audit-compliance/202*/??/*.log | wc -l)
Fichiers .tmp supprimés: 3
Taille backup: $(du -sh ~/backup/critical-logs/ | cut -f1)

Structure validée: ✅
Backup créé: ✅
Fichiers non-conformes supprimés: ✅

==========================================
EOF

cat ~/rapport-audit.txt
```

## 📊 Final Structure

/home/user/opt/audit-compliance/
├── 2023
│   ├── 09
│   │   └── (empty - .tmp deleted)
│   └── 12
│       ├── error_20231220.log
│       └── transaction_20231215.log
└── 2024
├── 01
│   └── transaction_20240103.log
├── 02
│   └── transaction_20240228.log
└── 03
└── error_20240310.log
8 directories, 5 .log files (3 .tmp files deleted)

## ✅ Evaluation Criteria

| Criterion | Weight | Score |
|-----------|--------|-------|
| Directory structure correct | 20% | 20/20 ✅ |
| All files migrated correctly | 30% | 30/30 ✅ |
| Backup created successfully | 20% | 20/20 ✅ |
| Non-compliant files deleted | 15% | 15/15 ✅ |
| Report accurate and complete | 15% | 15/15 ✅ |
| **TOTAL** | **100%** | **100/100** ✅ |

**Pass Threshold:** 85/100  
**Achieved Score:** 100/100  
**Result:** ✅ **PRODUCTION READY**

## 🎓 Key Learnings

### Technical Skills
- ✅ Directory hierarchy creation with `mkdir -p`
- ✅ File migration with verification
- ✅ Recursive copying with `cp -R`
- ✅ Safe deletion with `rm -i`
- ✅ Wildcard usage: `202*/??/*.tmp`

### Production Best Practices
- ✅ **Verify before executing:** Used `ls` and `tree` after each operation
- ✅ **Interactive deletion:** Used `rm -i` to prevent accidents
- ✅ **Documentation:** Created audit trail report
- ✅ **Backup first:** Created full backup before any destructive operations
- ✅ **Methodical approach:** Planned operation sequence before execution

### Industry Insights
- **Why this matters:** In banking, one wrong command can cause system-wide failures
- **Compliance is critical:** ISO 27001, GDPR, and financial regulations require strict log management
- **Zero-error tolerance:** Financial institutions cannot afford data loss or misplacement
- **Audit trails:** Every operation must be documented and traceable

## 🚀 Real-World Applications

This lab simulates scenarios commonly faced by SysAdmins in:
- 🏦 Banking & Financial Services
- 💳 Payment Processing Companies
- 📊 Financial Regulatory Bodies
- 🏛️ Government Financial Departments

## 📝 Commands Used
```bash
# Directory Management
mkdir -p ~/opt/audit-compliance/2023/{09,12}
tree ~/opt/audit-compliance/

# File Operations
mv transaction_20231215.log ~/opt/audit-compliance/2023/12/
cp -R ~/opt/audit-compliance/ ~/backup/critical-logs/
rm -i ~/opt/audit-compliance/202*/??/*.tmp

# Verification
ls -l
ls ~/opt/audit-compliance/202*/??/*.log | wc -l
du -sh ~/backup/critical-logs/

# Wildcards
ls *.tmp
ls 202*/??/*.tmp
ls transaction*
```

## 🔗 Resources

- [ISO 27001 Information Security Management](https://www.iso.org/isoiec-27001-information-security.html)
- [ACPR - French Banking Regulator](https://acpr.banque-france.fr/)
- [GDPR Compliance Guide](https://gdpr.eu/)

## 📅 Completion Details

- **Date Completed:** December 6, 2024
- **Time Taken:** ~2 hours (including documentation)
- **Environment:** Rocky Linux 9.x
- **Result:** ✅ 100/100 - Production Ready

---

**View the complete solution:** [solution.sh](./solution.sh)  
**Run setup script:** [setup.sh](./setup.sh)  
**Verify your work:** [verification.sh](./verification.sh)
