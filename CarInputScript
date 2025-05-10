using EVP;
using StarterAssets;
using UnityEngine;
using UnityEngine.InputSystem;

public class CarInputScript : MonoBehaviour
{

    [SerializeField] CarInput carInput;
    [SerializeField] float steering;
    [SerializeField] float throttle;
    public float handbrake;
    [SerializeField] bool handbrakeOverridesThrottle = false;
    [SerializeField] VehicleController target;
    [SerializeField] CarIgnition engine;
    [SerializeField] CarDoor driverSeat;
    // Start is called once before the first execution of Update after the MonoBehaviour is created
    void Awake()
    {
        carInput = new CarInput();
        target = GetComponent<VehicleController>();
        engine = GetComponent<CarIgnition>();
        driverSeat = GetComponentInChildren<CarDoor>();
    }

    void OnEnable()
    {
        carInput.Enable();
        carInput.Driving.Throttle.performed += ApplyThrottle;
        carInput.Driving.Throttle.canceled += ReleaseThrottle;
        carInput.Driving.Steering.performed += ApplySteering;
        carInput.Driving.Steering.canceled += ReleaseSteering;
        carInput.Driving.Handbrake.performed += ApplyHandbrake;
        carInput.Driving.Handbrake.canceled += ReleaseHandbrake;

    }

    void OnDisable()
    {
        carInput.Disable();
        if (target != null)
        {
            target.steerInput = 0.0f;
            target.throttleInput = 0.0f;
            target.brakeInput = 0.0f;
            target.handbrakeInput = 0.0f;
        }
    }

    void ApplyThrottle(InputAction.CallbackContext value)
    {
        throttle = value.ReadValue<float>();
    }

    void ReleaseThrottle(InputAction.CallbackContext value)
    {
        throttle = 0;
    }

    void ApplySteering(InputAction.CallbackContext value)
    {
        steering = value.ReadValue<float>();
    }

    void ReleaseSteering(InputAction.CallbackContext value)
    {
        steering = 0;
    }

    void ApplyHandbrake(InputAction.CallbackContext value)
    {
        handbrake = value.ReadValue<float>();
    }

    void ReleaseHandbrake(InputAction.CallbackContext value)
    {
        handbrake = 0;
    }

    // Update is called once per frame
    void FixedUpdate()
    {
        bool carRunning = engine.Running();
        bool inCar = driverSeat.InTheCar();
        
       
        target.steerInput = steering;
        
        

        float handbrakeInput = Mathf.Clamp01(handbrake);

        float forwardInput = Mathf.Clamp01(throttle);
        float reverseInput = Mathf.Clamp01(-throttle);


        float throttleInput = 0.0f;
        float brakeInput = 0.0f;

        float minSpeed = 0.1f;
        float minInput = 0.1f;

        if (carRunning)
        {
            if (target.speed > minSpeed)
            {
                throttleInput = forwardInput;
                brakeInput = reverseInput;
            }
            else
            {
                if (reverseInput > minInput)
                {
                    throttleInput = -reverseInput;
                    brakeInput = 0.0f;
                }
                else if (forwardInput > minInput)
                {
                    if (target.speed < -minSpeed)
                    {
                        throttleInput = 0.0f;
                        brakeInput = forwardInput;
                    }
                    else
                    {
                        throttleInput = forwardInput;
                        brakeInput = 0;
                    }
                }
            }
        }

        else
        {
            throttleInput = 0;
            brakeInput = reverseInput;
        }

        if (inCar)
        {
            if (handbrakeOverridesThrottle)
            {
                throttleInput *= 1.0f - handbrakeInput;
            }

            target.steerInput = steering;
            target.throttleInput = throttleInput;
            target.brakeInput = brakeInput;
            target.handbrakeInput = handbrakeInput;

            if (throttleInput < minInput && throttleInput > -0.1f && target.speed < 1f && target.speed > -1f && carRunning)
            {
                target.brakeInput = 1;
            }

            else if (!carRunning)
            {
                target.brakeInput = 0;
            }
        }

        
    }
}
