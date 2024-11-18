# Create Custom field called "Number of Locations" on the Account Object (Data Type=Number). Write a triqqer to create the number of contacts which are equal to the number which we will enter in the Number of Locations field on the Account Object.

## Trigger

trigger AccountTrigger on Account (before insert, after insert, before update, after update, before delete, after delete, after undelete){
    
    AccountTriggerHandler obj = new AccountTriggerHandler();
    obj.doAction();
 
}

## Handler

public class AccountTriggerHandler {
    
    List<Account> TriggerOld;
    List<Account> TriggerNew;
    Map<Id, Account> TriggerOldMap;
    Map<Id, Account> TriggerNewMap;
    
    public AccountTriggerHandler() //Constructor
    {
        TriggerOld = (List<Account>)Trigger.old;
        TriggerNew = (List<Account>)Trigger.new;
        TriggerOldMap = (Map<Id, Account>)Trigger.oldmap;
        TriggerNewMap = (Map<Id, Account>)Trigger.newmap;
    }
    
    public void doAction()
    {
        Switch on trigger.OperationType
        {
            When BEFORE_INSERT
            {
                
            }
            When BEFORE_UPDATE
            {

            }
            When AFTER_INSERT
            {
                caseStudy3_createContact();
            }
            When AFTER_UPDATE
            {
                caseStudy3_createContact();
            }
            When BEFORE_DELETE
            {

            }
            When AFTER_DELETE
            {

            }
            When AFTER_UNDELETE
            {
                
            }
        }
    }
    
    public void caseStudy3_createContact()
    {
        List<Id> accIds = new List<Id>();
        
        Decimal oldCount;
        Decimal newCount;
        Decimal contactsToAdd;
        
        List<Contact> contactsToCreate = new List<Contact>();
        
        for(Account accRec:TriggerNew)
        {
            if(accRec.NumberofLocations__c != Null)
            {
                accIds.add(accRec.Id);
                
                if(Trigger.isInsert)
                {
                    oldCount = 0;
                }
                else
                {
                    oldCount = TriggerOldMap.get(accRec.Id).NumberofLocations__c ?? 0;
                }
                
                newCount = accRec.NumberofLocations__c;
            }
            
            if(newCount != oldCount)
            {
                contactsToAdd = newCount - oldCount;
            }
   
            if(contactsToAdd>0)
            {
                createContacts(accRec, oldCount, contactsToAdd, contactsToCreate);
                /*for(Integer i=1; i<=contactsToAdd; i++)
                {
                    Contact con = new Contact();
                    con.LastName= TriggerNewMap.get(accIds[0]).Name+' Related Contact '+(oldCount+i);
                    con.AccountId = TriggerNewMap.get(accIds[0]).Id;
                    contactsToCreate.add(con);
                }*/
            }
        }
        
        if(contactsToCreate.size()>0)
        {
            Insert contactsToCreate;
        }
    }
    
    private static void createContacts(Account accRec, Decimal oldCount, Decimal contactsToAdd, List<Contact> contactsToCreate) 
    {
        for (Integer i = 1; i <= contactsToAdd; i++) 
        {
            Contact con = new Contact();
            con.LastName = accRec.Name + ' Related Contact ' + (oldCount + i);
            con.AccountId = accRec.Id;
            contactsToCreate.add(con);
        }
    }
