# Known Issues

## Release 0.5.0

No known issues.

## Release 0.1.4 

No known issues.

## Release 0.1.3 

No 0.1.3 release. 

## Release 0.1.2 

Uninstall sometimes hangs due to operator finalizer.  Solution is to delete the finalizer from the kappnav custom resource.

Procedure:  

1. open kappnav custom resource in resource editor

   ```
   kubectl edit kappnav instance -n kappnav
   ```

1. delete finalizer 

   Remove these two lines: 
   ```
   finalizers:
   - uninstall-helm-release
   ```
   
1. save edits 

Result should be that hanging uninstall completes successfully within a few moments. 
