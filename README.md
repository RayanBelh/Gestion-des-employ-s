 # Gestion d'employés en Java - Projet NetBeans

## Description
Ce projet est une application Java simple qui permet de gérer des employés dans une entreprise en utilisant JDBC pour interagir avec une base de données MySQL. Elle permet la création, l'affichage, la mise à jour et la suppression d'employés (développeurs et managers) dans une base de données.

## Structure du code
Le code est organisé en plusieurs packages :

### 1. **`ma.projet.connexion`**
- **`Connexion.java`** : Classe qui gère la connexion à la base de données MySQL.

### 2. **`ma.projet.beans`**
- **`Personne.java`** : Classe abstraite qui définit les attributs et méthodes de base pour un employé.
```java
public class Personne {
    private int id;
    private String nom;
    private double salaire;

    public Personne(int id, String nom, double salaire) {
        this.id = id;
        this.nom = nom;
        this.salaire = salaire;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getNom() {
        return nom;
    }

    public void setNom(String nom) {
        this.nom = nom;
    }

    public double getSalaire() {
        return salaire;
    }

    public void setSalaire(double salaire) {
        this.salaire = salaire;
    }
    
    @Override
     public String toString() {
        return "Personne{" +
                "id=" + id +
                ", nom='" + nom + '\'' +
                ", salaire=" + salaire +
                '}';
     }
}
```
- **`Developpeur.java`** : Classe héritant de `Personne`, représentant un développeur avec des attributs et méthodes spécifiques.
```java
public class Developpeur extends Personne {

    public Developpeur(int id, String nom, double salaire) {
        super(id, nom, salaire);
    }  
}
```
- **`Manager.java`** : Classe héritant de `Personne`, représentant un manager avec des attributs et méthodes spécifiques.
```java
public class Manager extends Personne {

    public Manager(int id, String nom, double salaire) {
        super(id, nom, salaire);
    } 
}
```

### 3. **`ma.projet.dao`**
- **`IDao.java`** : Interface définissant les méthodes CRUD (Create, Read, Update, Delete) pour un DAO (Data Access Object).
```java
package ma.projet.dao;

import java.util.List;
/**
 *
 * @author belha
 * @param <T>
 */
public interface IDao<T> {
    boolean create (T o);
    boolean update(T o);
    boolean delete(T o);
    T getById(int id);
    List<T> getAll();
    
}
```

### 4. **`ma.projet.service`**
- **`DeveloppeurService.java`** : Classe implémentant l'interface `IDao` pour gérer les opérations CRUD sur les développeurs.
```java
package ma.projet.service;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;
import java.util.logging.Level;
import java.util.logging.Logger;
import ma.projet.beans.Developpeur;
import ma.projet.connexion.Connexion;
import ma.projet.dao.IDao;

public class DeveloppeurService implements IDao<Developpeur> {

    @Override
    public boolean create(Developpeur o) {
         Connection connection = Connexion.getConnection(); // Get the connection
    if (connection == null) {
        // Handle the case where the connection is null
        System.err.println("Error: Could not establish a database connection.");
        return false; 
    } 
    try {
        String req = "insert into Developpeur (nom, salaire) values (?,?)";
        PreparedStatement ps = connection.prepareStatement(req);
        ps.setString(1, o.getNom());
        ps.setDouble(2, o.getSalaire());
        if (ps.executeUpdate() == 1) {
            return true;
        }
    } catch (SQLException ex) {
        Logger.getLogger(DeveloppeurService.class.getName()).log(Level.SEVERE, null, ex);
    } finally {
        // Close the connection (add this to all methods that use a connection)
        try {
            if (connection != null) {
                connection.close();
            }
        } catch (SQLException ex) {
            Logger.getLogger(DeveloppeurService.class.getName()).log(Level.SEVERE, null, ex);
        }
    }
    return false;
    }
    @Override
    public boolean update(Developpeur o) {
        try {
            String req = "update developpeur set nom = ?, salaire = ? where  id = ?";
            PreparedStatement ps
                    = Connexion.getConnection().prepareStatement(req);
            ps.setString(1, o.getNom());
            ps.setDouble(2, o.getSalaire());
            ps.setInt(3, o.getId());
            if (ps.executeUpdate() == 1) {
                return true;
            }
        } catch (SQLException ex) {
            Logger.getLogger(DeveloppeurService.class.getName()).log(Level.SEVERE, null, ex);
        }
        return false;
    }
    @Override
    public boolean delete(Developpeur o) {
         try {
            String req = "delete from developpeur where id = ?";
            PreparedStatement ps
                    = Connexion.getConnection().prepareStatement(req);
            ps.setInt(1, o.getId());
            if (ps.executeUpdate() == 1) {
                return true;
            }
        } catch (SQLException ex) {
            Logger.getLogger(DeveloppeurService.class.getName()).log(Level.SEVERE, null, ex);
        }
        return false;
    }
    @Override
    public Developpeur getById(int id) {
        Developpeur developpeur = null;
        try {
            String req = "select * from developpeur where id = ?";
            PreparedStatement ps
                    = Connexion.getConnection().prepareStatement(req);
            ps.setInt(1, id);
            ResultSet rs = ps.executeQuery();
            if (rs.next()) {
                developpeur = new Developpeur(rs.getInt("id"), rs.getString("nom"), rs.getDouble("salaire"));
            }
        } catch (SQLException ex) {
            Logger.getLogger(DeveloppeurService.class.getName()).log(Level.SEVERE, null, ex);
        }
        return developpeur;
    }
     @Override
    public List<Developpeur> getAll() {
         List<Developpeur> developpeur = new ArrayList<>();
        try {
            String req = " select * from developpeur ";
            PreparedStatement ps = 
                    Connexion.getConnection().prepareStatement(req);
           ResultSet rs = ps.executeQuery();
            while (rs.next()) {
                developpeur.add(new Developpeur(rs.getInt("id"), rs.getString("nom"), rs.getDouble("salaire")));
            }
        } catch (SQLException ex) {
            Logger.getLogger(DeveloppeurService.class.getName()).log(Level.SEVERE, null, ex);
        }
        return developpeur;
    }
}
```
- **`ManagerService.java`** : Classe implémentant l'interface `IDao` pour gérer les opérations CRUD sur les managers.
```java
package ma.projet.service;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;
import java.util.logging.Level;
import java.util.logging.Logger;
import ma.projet.beans.Manager;
import ma.projet.connexion.Connexion;
import ma.projet.dao.IDao;

public class ManagerService implements IDao<Manager> {

    @Override
    public boolean create(Manager o) {
        Connection connection = Connexion.getConnection(); // Get the connection
        if (connection == null) {
            // Handle the case where the connection is null
            System.err.println("Error: Could not establish a database connection.");
            return false;
        }
        try {
            String req = "insert into Manager (nom, salaire) values (?,?)";
            PreparedStatement ps = connection.prepareStatement(req);
            ps.setString(1, o.getNom());
            ps.setDouble(2, o.getSalaire());
            if (ps.executeUpdate() == 1) {
                return true;
            }
        } catch (SQLException ex) {
            Logger.getLogger(ManagerService.class.getName()).log(Level.SEVERE, null, ex);
        } finally {
            // Close the connection (add this to all methods that use a connection)
            try {
                if (connection != null) {
                    connection.close();
                }
            } catch (SQLException ex) {
                Logger.getLogger(ManagerService.class.getName()).log(Level.SEVERE, null, ex);
            }
        }
        return false;
    }
    @Override
    public boolean update(Manager o) {
       try {
            String req = "update manager set nom = ?, salaire = ? where  id = ?";
            PreparedStatement ps
                    = Connexion.getConnection().prepareStatement(req);
            ps.setString(1, o.getNom());
            ps.setDouble(2, o.getSalaire());
            ps.setInt(3, o.getId());
            if (ps.executeUpdate() == 1) {
                return true;
            }
        } catch (SQLException ex) {
            Logger.getLogger(ManagerService.class.getName()).log(Level.SEVERE, null, ex);
        }
        return false;
    }
    @Override
    public boolean delete(Manager o) {
       try {
            String req = "delete from manager where id = ?";
            PreparedStatement ps
                    = Connexion.getConnection().prepareStatement(req);
            ps.setInt(1, o.getId());
            if (ps.executeUpdate() == 1) {
                return true;
            }
        } catch (SQLException ex) {
            Logger.getLogger(ManagerService.class.getName()).log(Level.SEVERE, null, ex);
        }
        return false;
    }
    @Override
    public Manager getById(int id) {
        Manager manager = null;
        try {
            String req = "select * from manager where id = ?";
            PreparedStatement ps
                    = Connexion.getConnection().prepareStatement(req);
            ps.setInt(1, id);
            ResultSet rs = ps.executeQuery();
            if (rs.next()) {
                manager = new Manager(rs.getInt("id"), rs.getString("nom"), rs.getDouble("salaire"));
            }
        } catch (SQLException ex) {
            Logger.getLogger(ManagerService.class.getName()).log(Level.SEVERE, null, ex);
        }
        return manager;
    }

    @Override
    public List<Manager> getAll() {
        List<Manager> manager = new ArrayList<>();
        try {
            String req = " select * from manager ";
            PreparedStatement ps = 
                    Connexion.getConnection().prepareStatement(req);
           ResultSet rs = ps.executeQuery();
            while (rs.next()) {
                manager.add(new Manager(rs.getInt("id"), rs.getString("nom"), rs.getDouble("salaire")));
            }
        } catch (SQLException ex) {
            Logger.getLogger(ManagerService.class.getName()).log(Level.SEVERE, null, ex);
        }
        return manager;
    }   
}
```

### 5. **`ma.projet.test`**
- **`Entreprise.java`** : Classe principale qui contient la logique du programme, incluant la création et l'affichage des employés.
```java
package ma.projet.test;

import ma.projet.beans.Developpeur;
import ma.projet.beans.Manager;
import ma.projet.beans.Personne;
import ma.projet.service.DeveloppeurService;
import ma.projet.service.ManagerService;

public class Entreprise {

    public static void main(String[] args) {
        
        DeveloppeurService developpeurService = new DeveloppeurService();
        boolean dev1Created = developpeurService.create(new Developpeur(1, "Dev1", 3000));
        boolean dev2Created = developpeurService.create(new Developpeur(2, "Dev2", 3500));

        ManagerService managerService = new ManagerService();
        Manager manager = new Manager(3, "Manager", 5000); // Create Manager object
        boolean managerCreated = managerService.create(manager); // Create Manager in database

        System.out.println("Développeurs :");
        if (dev1Created) {
            System.out.println(new Developpeur(1, "Dev1", 3000));
        }
        if (dev2Created) {
            System.out.println(new Developpeur(2, "Dev2", 3500));
        }

        boolean dev3Created = developpeurService.create(new Developpeur(4, "Dev3", 4000));
        Personne directeurGeneral = new Personne(5, "Directeur Général", 10000);

        System.out.println("\n Hiérarchie des employés :");
        System.out.println("Directeur Général : " + directeurGeneral);
        if (managerCreated) {
            System.out.println("Manager : " + manager);
        }
        System.out.println("Développeurs : ");
        if (dev1Created) {
            System.out.println(new Developpeur(1, "Dev1", 3000));
        }
        if (dev2Created) {
            System.out.println(new Developpeur(2, "Dev2", 3500));
        }
        if (dev3Created) {
            System.out.println(new Developpeur(4, "Dev3", 4000));
        }
    }

}
```
## Fonctionnement de l'application

### Connexion à la base de données
L'application se connecte à une base de données MySQL nommée **`entreprise`** sur `localhost` en utilisant l'utilisateur `root` avec un mot de passe `root`. Les informations de connexion sont stockées dans le fichier `base.properties` situé dans le package `ma.projet.connexion` comme suit : 
```java
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost/entreprise
jdbc.username=root
jdbc.password=root
```
### Création d'employés
Le programme permet de créer des instances de `Developpeur` et de `Manager` , puis de les insérer dans la base de données.
### Affichage des employés
L'application affiche les informations des employés (développeurs et managers) en utilisant les méthodes `getAll()` des classes `DeveloppeurService` et `ManagerService`, ainsi que des employés (directeur général) comme des instances de la classe `Personne`. 

#### Exemple de sortie de la console :
```bsh
Développeurs :
Personne{id=1, nom='Dev1', salaire=3000.0}

Personne{id=2, nom='Dev2', salaire=3500.0}

Hiérarchie des employés :

Directeur Général : Personne{id=5, nom='Directeur Général', salaire=10000.0}

Manager : Personne{id=3, nom='Manager', salaire=5000.0}

Développeurs :

Personne{id=1, nom='Dev1', salaire=3000.0}

Personne{id=2, nom='Dev2', salaire=3500.0}

Personne{id=4, nom='Dev3', salaire=4000.0}
```

<span style="color:rouge">Ce texte est en bleu</span>
🔵 Texte en bleu
🔴 Texte en rouge

