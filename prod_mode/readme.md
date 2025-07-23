
---

### **Database Setup Guide**  
*(For when using SQL dumps with Dockerized NestJS/PostgreSQL)*  

#### **1. Critical Change Required**  
In `app.module.ts`:  
```typescript  
TypeOrmModule.forRoot({  
  // ... other config  
  synchronize: false, // ← MUST be false to prevent schema conflicts  
})  
```

#### **2. Deployment Steps**  
1. **Rebuild containers** (ensures clean state):  
   ```bash  
   docker-compose down -v  
   docker-compose up -d --build  
   ```  

2. **Import SQL dump**:  
   ```bash  
   # Copy dump to container  
   docker cp your_dump.sql autocar-db-1:/tmp/dump.sql  

   # Execute import  
   docker exec autocar-db-1 psql -U admin -d autocar -f /tmp/dump.sql  
   ```  

3. **Verify**:  
   ```bash  
   curl http://localhost:3000/cars  # Should return your data  
   ```  

#### **3. Why This Works**  
- `synchronize: false` prevents TypeORM from auto-creating tables  
- The SQL dump becomes the **single source of truth** for schema + data  
- Eliminates "already exists" errors during import  

#### **4. Maintenance Rules**  
- **To update the database**:  
  1. Generate new SQL dump from production  
  2. Repeat Steps 2-3  

- **Never** set `synchronize: true` again (it will conflict with dumped schema)  

---

### **Troubleshooting Cheatsheet**  
| Error | Solution |  
|-------|----------|  
| "relation already exists" | Always start with `docker-compose down -v` |  
| Empty API responses | Verify dump imported: `docker exec ... psql -c "SELECT COUNT(*) FROM car"` |  
| Connection refused | Check if DB is ready: `docker-compose logs db` |  

**One-liner for your team:**  
*"Set synchronize:false → docker-compose up → import dump.sql"*  

This keeps it simple while preventing schema conflicts. Let me know if you'd like to add any team-specific notes!
