# Write a trigger to prevent the users from deleting the Accounts. This is because System Administrator has all the permissions, we cannot change the permissions


## Answer using switch 

trigger AccountTrigger on Account (before insert, after insert, before update, after update, before delete, after delete, after undelete){    
    AccountTriggerHandler obj = new AccountTriggerHandler();
    obj.doAction();
 
}


## class
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
                
            }
            When AFTER_UPDATE
            {

            }
            When BEFORE_DELETE
            {
                caseStudy1_preventDelete();
            }
            When AFTER_DELETE
            {

            }
            When AFTER_UNDELETE
            {
                
            }
        }
    }
    
    public void caseStudy1_preventDelete()
    {
        Set<Id> systemAdminProfileIds = new Set<Id>();
        
        List<Profile> systemAdminProfiles = [SELECT Id FROM Profile WHERE Name = 'System Administrator'];
        
        for (Profile profile : systemAdminProfiles) 
        {
            systemAdminProfileIds.add(profile.Id);
        }
        
        if(!systemAdminProfileIds.contains(userInfo.getProfileId()))
        {
            for(Account accRec:TriggerOld)
            {
                accRec.addError('Cannot Delete Account Records');
            }   
        } 
    }	
}
